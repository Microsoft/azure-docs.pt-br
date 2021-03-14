---
title: Notas de versão para o agente de Sincronização de Arquivos do Azure | Microsoft Docs
description: Leia as notas de versão do agente de Sincronização de Arquivos do Azure, que permite centralizar os compartilhamentos de arquivos da sua organização nos arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/3/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 5549fc3b63b76c6158ae7399e6d94a43d2d4f28f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435181"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
Há suporte para as seguintes versões de agente de Sincronização de Arquivos do Azure:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
| Versão da v 11.2- [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 de fevereiro de 2021 | Com suporte |
| Versão v 11.1- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 de novembro de 2020 | Com suporte |
| V 10.1 versão- [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 de junho de 2020 | Com suporte-a versão do agente irá expirar em 7 de junho de 2021 |
| Pacote cumulativo de atualizações de maio de 2020 – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 de maio de 2020 | Com suporte-a versão do agente irá expirar em 7 de junho de 2021 |
| Versão V10 – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 de abril de 2020 | Com suporte-a versão do agente irá expirar em 7 de junho de 2021 |

## <a name="unsupported-versions"></a>Versões sem suporte
As seguintes versões do agente de Sincronização de Arquivos do Azure expiraram e não têm mais suporte:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
| Versão v9 | 9.0.0.0 - 9.1.0.0 | N/D | Sem suporte-versão do agente expirada em 16 de fevereiro de 2021 |
| Versão V8 | 8.0.0.0 | N/D | Sem suporte-versão do agente expirada em 12 de janeiro de 2021 |
| Versão v7 | 7.0.0.0-7.2.0.0 | N/D | Sem suporte-as versões do agente expiraram em 1º de setembro de 2020 |
| Versão V6 | 6.0.0.0 – 6.3.0.0 | N/D | Não compatível – As versões de agente expiraram em 21 de abril de 2020 |
| Versão V5 | 5.0.2.0 – 5.2.0.0 | N/D | Não compatível – As versões de agente expiraram em 18 de março de 2020 |
| Lançamento V4 | 4.0.1.0 – 4.3.0.0 | N/D | Não compatível – As versões de agente expiraram em 6 de novembro de 2019 |
| Versão V3 | 3.1.0.0 – 3.4.0.0 | N/D | Não compatível – As versões de agente expiraram em 19 de agosto de 2019 |
| Agentes pré-GA | 1.1.0.0 – 3.0.13.0 | N/D | Sem suporte – versão de agente expiradas em 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>Versão do agente 11.2.0.0
As notas de versão a seguir são para a versão 11.2.0.0 do agente de Sincronização de Arquivos do Azure lançado em 2 de fevereiro de 2021. Essas notas são além das notas de versão listadas para a versão 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos 
- Se uma sessão de sincronização for cancelada devido a um grande número de erros por item, a sincronização poderá passar pela reconciliação quando uma nova sessão for iniciada se o serviço de Sincronização de Arquivos do Azure determinar uma sessão de sincronização personalizada é necessária para corrigir os erros por item.
- O registro de um servidor usando o cmdlet Register-AzStorageSyncServer pode falhar com o erro "exceção sem tratamento".
- Novo cmdlet do PowerShell (Add-StorageSyncAllowedServerEndpointPath) para configurar os caminhos de pontos de extremidade do servidor permitidos em um servidor. Esse cmdlet é útil para cenários nos quais a implantação de Sincronização de Arquivos do Azure é gerenciada por um provedor de soluções de nuvem (CSP) ou provedor de serviços e o cliente deseja configurar caminhos de pontos de extremidade de servidor permitidos em um servidor. Ao criar um ponto de extremidade do servidor, se o caminho especificado não estiver na lista de permissões, a criação do ponto de extremidade do servidor falhará. Observe que esse é um recurso opcional e todos os caminhos com suporte são permitidos por padrão ao criar um ponto de extremidade do servidor.  

    
    - Para adicionar um caminho de ponto de extremidade de servidor permitido, execute os seguintes comandos do PowerShell no servidor:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Para obter a lista de caminhos com suporte, execute o seguinte comando do PowerShell:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Para remover um caminho, execute o seguinte comando do PowerShell:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Versão do agente 11.1.0.0
As notas de versão a seguir são para a versão 11.1.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 4 de novembro de 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos
- Novos modos de camadas de nuvem para controlar o download inicial e a recuperação proativa
    - Modo de download inicial: agora você pode escolher como deseja que os arquivos sejam baixados inicialmente em seu novo ponto de extremidade do servidor. Deseja que todos os arquivos sejam em camadas ou quantos arquivos forem possíveis de serem baixados em seu servidor pelo carimbo de data/hora da última modificação? Você pode fazer isso! Não é possível usar camadas de nuvem? Agora você pode optar por evitar arquivos em camadas no seu sistema. Para saber mais, confira a seção [criar um ponto de extremidade do servidor](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) na documentação implantar sincronização de arquivos do Azure.
    - Modo de cancelamento proativo: sempre que um arquivo é criado ou modificado, você pode recancelá-lo proativamente nos servidores que você especificar dentro do mesmo grupo de sincronização. Isso torna o arquivo prontamente disponível para consumo em cada servidor especificado. Você tem equipes em todo o mundo trabalhando nos mesmos dados? Habilite o cancelamento proativo para que, quando a equipe chegar à manhã seguinte, todos os arquivos atualizados por uma equipe em um fuso horário diferente sejam baixados e estejam prontos para uso! Para saber mais, confira [recalls de forma proativa arquivos novos e alterados de uma seção compartilhamento de arquivos do Azure](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) na documentação implantar sincronização de arquivos do Azure.

- Excluir aplicativos do controle de hora do último acesso em camadas de nuvem agora você pode excluir aplicativos do último controle de tempo de acesso. Quando um aplicativo acessa um arquivo, a hora do último acesso do arquivo é atualizada no banco de dados de camadas de nuvem. Os aplicativos que examinam o sistema de arquivos como antivírus fazem com que todos os arquivos tenham o mesmo último tempo de acesso que impacta quando os arquivos são em camadas.

    Para excluir aplicativos do último controle de tempo de acesso, adicione o nome do processo à configuração do registro HeatTrackingProcessNameExclusionList que está localizada em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Exemplo: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > A eliminação de duplicação de dados e os processos FSRM (Gerenciador de recursos de servidor de arquivos) são excluídos por padrão (embutido em código) e a lista de exclusão de processos é atualizada a cada 5 minutos.

- Diversos aprimoramentos de desempenho e confiabilidade
    - Desempenho aprimorado de detecção de alterações para detectar arquivos que foram alterados no compartilhamento de arquivos do Azure.
    - Desempenho de upload de sincronização aprimorado.
    - O carregamento inicial agora é executado de um instantâneo VSS que reduz erros por item e falhas de sessão de sincronização.
    - As melhorias de confiabilidade de sincronização para determinados padrões de e/s.
    - Correção de um bug para impedir que o banco de dados de sincronização volte a ficar em clusters de failover quando ocorrer um failover.
    - Melhor desempenho de recuperação ao acessar um arquivo em camadas.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](./storage-sync-files-planning.md#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é compatível com a opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória. Confira [Recursos do sistema recomendados](./storage-sync-files-planning.md#recommended-system-resources) para obter mais informações.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há compatibilidade com a execução do sysprep em um servidor que tenha o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
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
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure, o cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivo do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos, uma assinatura ou um locatário do Azure AD diferente. Depois que o serviço de sincronização de armazenamento ou a conta de armazenamento for movido, você precisará conceder ao aplicativo Microsoft. StorageSync acesso à conta de armazenamento (consulte [garantir que sincronização de arquivos do Azure tenha acesso à conta de armazenamento](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Ao criar o ponto de extremidade na nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo locatário do Azure AD. Depois que o ponto de extremidade na nuvem é criado, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser movidos para diferentes locatários do Azure AD.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
    > [!Warning]  
    > Não há suporte para a opção Robocopy/B com Sincronização de Arquivos do Azure. Usando a opção Robocopy/B com um ponto de extremidade do Sincronização de Arquivos do Azure Server como a origem pode levar ao arquivo corrompido.

## <a name="agent-version-10100"></a>Versão do agente 10.1.0.0
As notas de versão a seguir são para a versão 10.1.0.0 do agente de Sincronização de Arquivos do Azure lançado em 5 de junho de 2020. Essas observações são além das notas de versão listadas para a versão 10.0.0.0 e 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte ao ponto de extremidade privado do Azure
    - O tráfego de sincronização para o serviço de sincronização de armazenamento agora pode ser enviado para um ponto de extremidade privado. Isso habilita o túnel em uma conexão de ExpressRoute ou VPN. Para saber mais, confira [Configurando pontos de extremidade de rede sincronização de arquivos do Azure](./storage-sync-files-networking-endpoints.md).
- Arquivos sincronizados a métrica agora exibirá o progresso enquanto uma sincronização grande estiver em execução, em vez de no final.
- Melhorias de confiabilidade diversas para a instalação do agente, camadas de nuvem, sincronização e telemetria

## <a name="agent-version-10020"></a>Versão do agente 10.0.2.0
As notas sobre a versão a seguir são da versão 10.0.2.0 do agente de Sincronização de Arquivos do Azure lançada em 19 de maio de 2020. Estas notas complementam as notas sobre a versão listadas para a versão 10.0.0.0.

Problema corrigido nessa versão:  
- O Agente de Sincronização de Armazenamento (FileSyncSvc) falha com frequência após a instalação do agente da Sincronização de Arquivos do Azure v10.

> [!Note]  
>Essa versão não foi habilitada para servidores configurados para atualizar automaticamente quando uma nova versão é disponibilizada. Para instalar essa atualização, use o Microsoft Update ou Catálogo de Atualizações da Microsoft (confira [KB4522412](https://support.microsoft.com/help/4522412) para ver as instruções de instalação).

## <a name="agent-version-10000"></a>Versão do agente 10.0.0.0
As notas sobre a versão a seguir são para a versão 10.0.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 9 de abril de 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Progresso da sincronização aprimorado no portal
    - Com a versão do agente v10, o portal do Azure em breve começará a mostrar o tipo de sessão de sincronização em execução. Por ex.: download inicial, download regular, recall em segundo plano (casos rápidos de recuperação de desastre) e semelhantes. 

- Experiência aprimorada no portal de camada de nuvem
    - Se você tiver arquivos que estão falhando na camada ou no recall, agora você poderá exibir os erros de camadas nas propriedades do ponto de extremidade do servidor.
    - Informações adicionais de camada de nuvem estão disponíveis para um ponto de extremidade do servidor:
        - Tamanho do cache local
        - Eficiência de uso do cache
        - Detalhes da política de camada de nuvem: tamanho do volume, espaço livre atual ou hora do último acesso ao arquivo mais antigo no cache local.
    - Essas alterações serão disponibilizadas no portal do Azure logo após a versão inicial do agente v10.

- Suporte para mover o serviço de sincronização de armazenamento e/ou a conta de armazenamento para um locatário Azure Active Directory diferente
    - A Sincronização de Arquivos do Azure agora é compatível com o recurso de mover o Serviço de Sincronização de Armazenamento e/ou a conta de armazenamento para um grupo de recursos, assinatura ou locatário do Azure AD.
    
- Diversos aprimoramentos de desempenho e confiabilidade
    - A detecção de alteração no compartilhamento de arquivo do Azure poderá falhar se a VNET (rede virtual) e as regras de firewall estiverem configuradas na conta de armazenamento.
    - Consumo de memória reduzido associado ao recall. 
    - Desempenho aprimorado ao usar o cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
    - Outros aprimoramentos de confiabilidade. 
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](./storage-sync-files-planning.md#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é compatível com a opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há compatibilidade com a execução do sysprep em um servidor que tenha o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
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
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure, o cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivo do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos, uma assinatura ou um locatário do Azure AD diferente. Depois que o serviço de sincronização de armazenamento ou a conta de armazenamento for movido, você precisará conceder ao aplicativo Microsoft. StorageSync acesso à conta de armazenamento (consulte [garantir que sincronização de arquivos do Azure tenha acesso à conta de armazenamento](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Ao criar o ponto de extremidade na nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo locatário do Azure AD. Depois que o ponto de extremidade na nuvem é criado, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser movidos para diferentes locatários do Azure AD.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
