---
title: Notas de versão para o agente de Sincronização de Arquivos do Azure | Microsoft Docs
description: Notas de versão para o agente de Sincronização de Arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 6/27/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: ba7b8ae5c7e68c22b83583de6cd0b84f621d4b25
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786661"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm suporte pela Sincronização de arquivos do Azure:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
| V7 Versão - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 de junho de 2019 | [Flighting](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#automatic-agent-lifecycle-management) |
| Junho de 2019 cumulativo de atualizações - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 de junho de 2019 | Com suporte (versão recomendada) |
| Junho de 2019 cumulativo de atualizações - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 de junho de 2019 | Com suporte |
| Maio de 2019 cumulativo de atualizações - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 de maio de 2019 | Com suporte |
| V6 Versão - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 de abril de 2019 | Com suporte |
| Abril de 2019 cumulativo de atualizações - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 de abril de 2019 | Com suporte |
| Março de 2019 cumulativo de atualizações - [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 de março de 2019 | Com suporte |
| Versão V5 – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 de fevereiro de 2019 | Com suporte |
| Pacote cumulativo de atualizações de janeiro de 2019 – [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 de janeiro de 2019 | Com suporte |
| Pacote cumulativo de atualizações de dezembro de 2018 – [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 de dezembro de 2018 | Com suporte |
| Pacote cumulativo de atualizações de dezembro de 2018 | 4.1.0.0 | 4 de dezembro de 2018 | Com suporte |
| Lançamento V4 | 4.0.1.0 | 13 de novembro de 2018 | Com suporte |
| Pacote cumulativo de atualizações de setembro de 2018 | 3.3.0.0 | 24 de setembro de 2018 | Compatível – A versão do agente expirará em 19 de julho de 2019 |
| Pacote cumulativo de atualizações de agosto de 2018 | 3.2.0.0 | 15 de agosto de 2018 | Compatível – A versão do agente expirará em 19 de julho de 2019 |
| Disponibilidade Geral | 3.1.0.0 | 19 de julho de 2018 | Compatível – A versão do agente expirará em 19 de julho de 2019 |
| Agentes expirados | 1.1.0.0 – 3.0.13.0 | N/D | Sem suporte – versão de agente expiradas em 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-7000"></a>Versão do agente 7.0.0.0
As notas de versão a seguir são para a versão 7.0.0.0 do agente de sincronização de arquivo do Azure (lançada em 19 de junho de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para tamanhos maiores de compartilhamento de arquivo
    - Com a visualização de compartilhamentos de arquivos maiores, estamos aumentando os limites de suporte para sincronização de arquivos também. Nessa primeira etapa, a sincronização de arquivos do Azure agora dá suporte a até 25TB e 50million arquivos em um único namespace de sincronização. Para aplicar para a visualização de compartilhamento de arquivos grandes, preencha este formulário https://aka.ms/azurefilesatscalesurvey. 
- Restauração de nível de arquivo de Backup do Azure aprimorada
    - Arquivos individuais restaurados usando o Backup do Azure agora são detectados e sincronizados com o ponto de extremidade do servidor com mais rapidez.
- Disposição em camadas recall cmdlet Confiabilidade aprimorada na nuvem 
    - A cmdlet de cancelamento (Invoke-StorageSyncFileRecall) de camadas de nuvem agora dá suporte a por arquivo de contagem de repetições e intervalo de repetição, semelhante ao robocopy.
- Suporte para o TLS 1.2 (TLS 1.0 e 1.1 está desabilitado)
    - Azure sincronização de arquivos agora dá suporte a usando o TLS 1.2 somente em servidores que têm o TLS 1.0 e 1.1 desabilitado. Antes dessa melhoria, o registro do servidor falharia se TLS 1.0 e 1.1 foi desabilitado no servidor.
- Diversos melhorias no desempenho e confiabilidade para sincronizar e camadas de nuvem
    - Há várias melhorias de confiabilidade e desempenho nesta versão. Alguns deles são direcionados para melhorar as camadas mais eficiente de nuvem e sincronização de arquivos do Azure como um todo trabalho nessas situações quando você tem uma limitação do conjunto de agendamento de largura de banda.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- Não há suporte para o agente na opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-6300"></a>Versão do agente 6.3.0.0
As notas de versão a seguir são para a versão 6.3.0.0 do agente de sincronização de arquivo do Azure lançada em 27 de junho de 2019. Estas notas se complementam as notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Acessando ou procurar um local de ponto de extremidade de servidor via SMB é lento no Windows Server 2012 R2 
- Aumento da utilização da CPU depois de instalar o agente de sincronização de arquivos do Azure v6
- Melhorias de telemetria em camadas na nuvem

## <a name="agent-version-6200"></a>Versão do agente 6.2.0.0
As notas de versão a seguir são para a versão 6.2.0.0 do agente de sincronização de arquivo do Azure lançada em 13 de junho de 2019. Estas notas se complementam as notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Depois de criar um ponto de extremidade do servidor, o uso de CPU alta pode ocorrer quando o recall do plano de fundo está baixando arquivos para o servidor
- Sincronização e as camadas de operações de nuvem podem falhar com o erro ECS_E_SERVER_CREDENTIAL_NEEDED devido à expiração do token
- Recuperar um arquivo poderá falhar se a URL para baixar o arquivo contém caracteres reservados 

## <a name="agent-version-6100"></a>Versão do agente 6.1.0.0
As notas de versão a seguir são para a versão 6.1.0.0 do agente de sincronização de arquivo do Azure lançada em 6 de maio de 2019. Estas notas se complementam as notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Windows Admin Center não pode exibir a configuração de ponto de extremidade de versão e o servidor do agente em servidores que têm a versão 6.0 instalado do agente de sincronização de arquivos do Azure.

## <a name="agent-version-6000"></a>Versão do agente 6.0.0.0
As notas de versão a seguir são para a versão 6.0.0.0 do agente de sincronização de arquivo do Azure (lançada em 22 de abril de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte de atualização automática do agente
  - Nós ouvimos seus comentários e adicionado a um recurso de atualização automática para o agente de servidor de sincronização de arquivos do Azure. Para obter mais informações, consulte [política de atualização do agente de sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Suporte para arquivos do Azure compartilham as ACLs
  - A sincronização de arquivos do Azure sempre forneceu suporte para ACLs a sincronização entre pontos de extremidade do servidor, mas as ACLs não foram sincronizadas com o ponto de extremidade de nuvem (compartilhamento de arquivos do Azure). Essa versão adiciona suporte para ACLs de sincronização entre o servidor e pontos de extremidade de nuvem.
- Carregamento em paralelo e baixar as sessões de sincronização para um ponto de extremidade do servidor 
  - Pontos de extremidade do servidor agora dão suporte a upload e download de arquivos ao mesmo tempo. Não precisa mais esperar um download concluir a arquivos então pode ser carregado para o compartilhamento de arquivos do Azure. 
- Novos cmdlets de camada na nuvem para obter o volume e o status de disposição em camadas
  - Dois cmdlets do PowerShell novo, o local do servidor agora pode ser usados para obter informações de recall de disposição em camadas e arquivo de nuvem. Eles disponibilizar informações de registro em log de dois canais de evento no servidor:
    - Get-StorageSyncFileTieringResult listará todos os arquivos e seus caminhos que ainda não em camadas e relatórios sobre o motivo por que.
    - Get-StorageSyncFileRecallResult relata todos os eventos de recall do arquivo. Ele lista todos os arquivos recuperados e seu caminho, bem como o sucesso ou erro para esse recall.
  - Por padrão, os canais de evento podem armazenar até 1MB cada – você pode aumentar a quantidade de arquivos relatados, aumentando o tamanho do canal de evento.
- Suporte para o modo FIPS
  - A sincronização de arquivos do Azure agora dá suporte a habilitar o modo FIPS em servidores que têm o agente de sincronização de arquivos do Azure instalado.
    - Antes de habilitar o modo FIPS em seu servidor, instale o agente de sincronização de arquivos do Azure e [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) em seu servidor. Se o FIPS já estiver habilitado no servidor, [baixar manualmente](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) as [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) ao seu servidor.
- Melhorias de confiabilidade de diversos para sincronização e camadas de nuvem

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- Não há suporte para o agente na opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.

## <a name="agent-version-5200"></a>Versão do agente 5.2.0.0
As notas de versão a seguir são para a versão 5.2.0.0 do agente de sincronização de arquivo do Azure lançada em 4 de abril de 2019. Estas notas se complementam as notas de versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Recursos de reinício de melhorias de confiabilidade para transferência de dados offline e transferência de dados
- Melhorias de telemetria de sincronização

## <a name="agent-version-5100"></a>Versão do agente 5.1.0.0
As notas de versão a seguir são para a versão 5.1.0.0 do agente de sincronização de arquivo do Azure lançada em 7 de março de 2019. Estas notas se complementam as notas de versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Arquivos talvez não consiga sincronizar com o erro 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se a enumeração de alteração estiver falhando no servidor
- Se um arquivo ou uma sessão de sincronização recebe um erro 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), sincronização agora tentará repetir a operação
- Arquivos talvez não consiga sincronizar com o erro 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Alto uso de memória pode ocorrer ao recuperar arquivos
- Melhorias de telemetria em camadas na nuvem 

## <a name="agent-version-5020"></a>Versão do agente 5.0.2.0
As notas sobre a versão a seguir são para a versão 5.0.2.0 do agente de Sincronização de Arquivos do Azure (lançada em 12 de fevereiro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para a nuvem do Azure Governamental
  - Adicionamos suporte à versão prévia para a nuvem do Azure Governamental. Isso exige uma assinatura inclusa na lista de permissão e download do agente especial da Microsoft. Para obter acesso à versão prévia, envie um email diretamente para [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Suporte para Eliminação de Duplicação de Dados
    - A Eliminação de Duplicação de Dados tem suporte completo com a camada de nuvem habilitada no Windows Server 2016 e no Windows Server 2019. Habilitar a eliminação de duplicação em um volume com camada de nuvem habilitada permite armazenar em cache mais arquivos localmente sem ter que provisionar mais armazenamento.
- Suporte para transferência de dados offline (por exemplo, por meio do Data Box)
    - Migre facilmente grandes quantidades de dados para a Sincronização de Arquivos do Azure do modo que escolher. Você pode escolher o Azure Data Box, o AzCopy e até mesmo serviços de migração de terceiros. Não é necessário usar grandes quantidades de largura de banda para colocar seus dados no Azure. No caso do Data Box, simplesmente envie-os por ele. Para obter mais informações, confira [Documentos de transferência de dados offline](https://aka.ms/AFS/OfflineDataTransfer).
- Desempenho de sincronização aprimorado
    - Os clientes com vários pontos de extremidade do servidor no mesmo volume podem ter passado por sincronização lenta antes dessa versão. O Azure File Sync cria um instantâneo temporário do VSS uma vez por dia no servidor para sincronizar arquivos que tenham identificadores abertos. A Sincronização agora dá suporte a vários pontos de extremidade de servidor sincronizando em um volume quando uma sessão de sincronização do VSS estiver ativa. Não é mais necessário esperar a conclusão de uma sessão de sincronização do VSS para retomar a sincronização em outros pontos de extremidade do servidor no volume.
- Monitoramento aprimorado no portal
    - Gráficos foram adicionados no portal do Serviço de Sincronização de Armazenamento para exibir:
        - Número de arquivos sincronizados
        - Tamanho dos dados transferidos
        - Número de arquivos não sincronizados
        - Tamanho dos dados em recall
        - Status da conectividade do servidor
    - Para saber mais, confira [Monitorar a Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Confiabilidade e escalabilidade aprimoradas
    - O número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório aumentou para 1 milhão. O limite anterior era de 200.000.
    - A Sincronização tentará retomar a transferência de dados em vez de retransmitir quando uma transferência for interrompida para arquivos grandes 

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.
- O modo FIPS não tem suporte e deve ser desabilitado. 

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.

## <a name="agent-version-4300"></a>Versão do agente 4.3.0.0
As notas sobre a versão a seguir são referentes à versão 4.3.0.0 do agente de Sincronização de Arquivos do Azure lançada em 14 de janeiro de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 4.0.1.0.

Lista dos problemas corrigidos nesta versão:  
- Arquivos não são organizadas em camadas depois de atualizar o agente de Sincronização de Arquivos do Azure para a versão 4.x.
- Agora, AfsUpdater.exe tem suporte no Windows Server 2019.
- Diversas melhorias de confiabilidade para sincronização. 

## <a name="agent-version-4200"></a>Versão do agente 4.2.0.0
As notas sobre a versão a seguir são para a versão 4.2.0.0 do agente de Sincronização de Arquivos do Azure lançada em 10 de dezembro de 2018. Estas notas complementam as notas sobre a versão listadas para a versão 4.0.1.0.

Lista dos problemas corrigidos nesta versão:  
- Um erro de parada 0x3B ou erro de parada 0x1E pode ocorrer quando um instantâneo do VSS é criado.  
- Pode ocorrer uma perda de memória quando a camada de nuvem está habilitada  

## <a name="agent-version-4100"></a>Versão do agente 4.1.0.0
As notas sobre a versão a seguir são para a versão 4.1.0.0 do agente de Sincronização de Arquivos do Azure lançada em 4 de dezembro de 2018. Estas notas complementam as notas sobre a versão listadas para a versão 4.0.1.0.

Lista dos problemas corrigidos nesta versão:  
- O servidor pode ficar sem resposta devido a uma perda de memória da camada de nuvem.  
- A instalação do agente falha com o seguinte erro: Erro 1921. Não foi possível parar o serviço de 'agente de sincronização de armazenamento’ (FileSyncSvc).  Verifique se você tem privilégios suficientes para parar serviços do sistema.  
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) pode falhar quando o uso de memória é alto.  
- Diversas melhorias de confiabilidade para camada e sincronização de nuvem.

## <a name="agent-version-4010"></a>Versão de agente 4.0.1.0
As notas sobre a versão a seguir são para a versão 4.0.1.0 do agente de Sincronização de Arquivos do Azure (lançada em 13 de novembro de 2018).

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.
- O modo FIPS não tem suporte e deve ser desabilitado. 
- Um erro de parada 0x3B ou erro de parada 0x1E pode ocorrer quando um instantâneo do VSS é criado.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.
- A Eliminação de duplicação de dados e as camadas de nuvem não têm suporte no mesmo volume.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- A configuração de política de camada de nuvem baseada em data é usada para especificar arquivos que devem ser armazenados em cache, se acessados em um número de dias especificado. Para saber mais, consulte [Visão geral da camada de nuvem](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.

## <a name="agent-version-3300"></a>Versão de agente 3.3.0.0
As notas sobre a versão a seguir são para a versão 3.3.0.0 do agente de Sincronização de Arquivos do Azure lançada em 24 de setembro de 2018. Estas notas complementam as notas de versão listadas para a versão 3.1.0.0.

Lista dos problemas corrigidos nesta versão:
- O estado do servidor registrado será "Aparece offline" depois que o agente de Sincronização de Arquivos do Azure for atualizado para a versão 3.1 ou 3.2.
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) apresenta falhas devido a arquivos com caminhos longos.
- O registro do servidor falha com o erro: não foi possível carregar o arquivo ou o assembly Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Versão do agente 3.2.0.0
As notas de versão a seguir são para a versão 3.2.0.0 do agente de Sincronização de Arquivos do Azure lançada em 15 de agosto de 2018. Estas notas complementam as notas de versão listadas para a versão 3.1.0.0.

Essa versão inclui as seguintes correções:
- Falha na sincronização com erro de memória insuficiente (0x8007000e) devido à perda de memória

## <a name="agent-version-3100"></a>Versão do agente 3.1.0.0
As notas de versão a seguir são para a versão 3.1.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 19 de julho de 2018).

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte somente pelo Windows Server 2016 e pelo Windows Server 2012 R2.
- O agente requer pelo menos 2 GB de memória física.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.
- O modo FIPS não tem suporte e deve ser desabilitado. 

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- Não use o FSRM (Gerenciador de Recursos de Servidor de Arquivos) ou outras triagens de arquivo. As triagens de arquivo podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido a elas.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. A instalação do agente e o registro do servidor devem ocorrer depois da implantação da imagem do servidor e da conclusão da mini-instalação do sysprep.
- A Eliminação de duplicação de dados e as camadas de nuvem não têm suporte no mesmo volume.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas poderão ficar inutilizáveis se os arquivos não forem recuperados antes da exclusão do ponto de extremidade do servidor.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um sistema operacional ou o arquivo de paginação do aplicativo que esteja dentro de um ponto de extremidade de servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
