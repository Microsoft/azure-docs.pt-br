---
title: Notas de versão para o agente de Sincronização de Arquivos do Azure | Microsoft Docs
description: Leia as notas de versão do agente de Sincronização de Arquivos do Azure, que permite centralizar os compartilhamentos de arquivos da sua organização nos arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 2/11/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 4e1b5828e096d3d712def5420fd846d65b22e696
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366102"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
Há suporte para as seguintes versões de agente de Sincronização de Arquivos do Azure:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
| Versão da v 11.2- [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 de fevereiro de 2021 | Com suporte-comprovante |
| Versão v 11.1- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 de novembro de 2020 | Com suporte |
| V 10.1 versão- [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 de junho de 2020 | Com suporte |
| Pacote cumulativo de atualizações de maio de 2020 – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 de maio de 2020 | Com suporte |
| Versão V10 – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 de abril de 2020 | Com suporte |
| Pacote cumulativo de atualizações de dezembro de 2019 – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 de dezembro de 2019 | Com suporte-a versão do agente irá expirar em 16 de fevereiro de 2021 |
| Versão V9 – [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 de dezembro de 2019 | Com suporte-a versão do agente irá expirar em 16 de fevereiro de 2021 |

## <a name="unsupported-versions"></a>Versões sem suporte
As seguintes versões do agente de Sincronização de Arquivos do Azure expiraram e não têm mais suporte:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
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

## <a name="agent-version-9100"></a>Versão do agente 9.1.0.0
As notas sobre a versão a seguir são para a versão 9.1.0.0 do agente de Sincronização de Arquivos do Azure lançada em 12 de dezembro de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 9.0.0.0.

Problema corrigido nessa versão:  
- A sincronização falha com um dos seguintes erros após a atualização para o agente 9.0 da Sincronização de Arquivos do Azure:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Versão do agente 9.0.0.0
As notas sobre a versão a seguir são para a versão 9.0.0.0 do agente de Sincronização de Arquivos do Azure lançada em 2 de dezembro de 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Compatibilidade com a restauração de autoatendimento
    - Os usuários agora podem também restaurar arquivos em camadas (juntamente com arquivos em disco) usando o recurso de versão anterior, dos instantâneos do VSS que são criados após o recurso de restauração de autoatendimento ser habilitado no volume. Antes da versão V9, não havia suporte para o recurso de versão anterior para arquivos em camadas. Esse recurso deve ser habilitado para cada volume separadamente, em que um ponto de extremidade com camada de nuvem habilitada existe. Para obter mais informações, consulte:  
[A restauração de autoatendimento por meio de versões anteriores e do VSS (Serviço de Cópias de Sombra de Volume)](./storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Compatibilidade com tamanhos maiores de compartilhamento de arquivo 
    - A Sincronização de Arquivos do Azure agora é compatível com até 64 TiB e 100 milhões de arquivos em um único namespace de sincronização.  
 
- Compatibilidade com a eliminação de duplicação de dados no Server 2019 
    - A eliminação de duplicação de dados agora tem suporte (independentemente de a disposição em camadas da nuvem estar habilitada ou desabilitada em um ou mais pontos de extremidade do servidor no volume) no Windows Server 2016 e no Windows Server 2019. Para dar suporte à eliminação de duplicação de dados em volumes com camadas de nuvem no servidor 2019, o Windows Update [KB4520062](https://support.microsoft.com/help/4520062) deve estar instalado. 
 
- Aumento do tamanho do arquivo mínimo para um arquivo ser colocado em camadas 
    - O tamanho do arquivo mínimo para um arquivo ser colocado em camadas agora é baseado no tamanho do cluster do sistema de arquivos (duas vezes o tamanho do cluster de sistema de arquivos). Por exemplo, por padrão, o tamanho do cluster do sistema de arquivos NTFS é de 4 KB, o tamanho mínimo resultante para um arquivo ser colocado em camadas é de 8 KB. 
 
- Cmdlet de teste da conectividade de rede 
    - Como parte da configuração da Sincronização de Arquivos do Azure, vários pontos de extremidade de serviço devem ser contatados. Cada um deles tem seu próprio nome DNS que precisa ser acessível ao servidor. Essas URLs também são específicas para a região em que um servidor está registrado. Depois que um servidor é registrado, o cmdlet de teste de conectividade (utilitário de registro do PowerShell e do servidor) pode ser usado para testar as comunicações com todas as URLs específicas desse servidor. Esse cmdlet pode ajudar a solucionar problemas quando a comunicação incompleta impede que o servidor trabalhe totalmente com Sincronização de Arquivos do Azure e pode ser usado para ajustar as configurações de proxy e firewall.  
 
        Para executar o teste de conectividade de rede, execute os seguintes comandos do PowerShell: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Aprimoramento ao remover o ponto de extremidade do servidor quando a camada de nuvem está habilitada 
    - Como antes, remover um ponto de extremidade do servidor não resulta na remoção de arquivos no compartilhamento de arquivo do Azure. No entanto, o comportamento de pontos de nova análise no servidor local foi alterado. Pontos de nova análise (ponteiros para arquivos que não são locais no servidor) agora são excluídos durante a remoção de um ponto de extremidade do servidor. Os arquivos totalmente armazenados em cache permanecerão no servidor. Esse aprimoramento foi feito para impedir [arquivos em camadas órfãos](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) ao remover um ponto de extremidade do servidor. Se o ponto de extremidade do servidor for recriado, os pontos de nova análise para os arquivos em camadas serão recriados no servidor.  
 
- Aprimoramento de desempenho e confiabilidade 
    - Redução nas falhas de recall. O tamanho de recall agora é ajustado automaticamente com base na largura de banda da rede. 
    - Melhor desempenho de download ao adicionar um novo servidor a um grupo de sincronização. 
    - Arquivos reduzidos não sincronizam devido a conflitos de restrição. 
    - Os arquivos falharão na camada ou terão um recall inesperado em determinados cenários se o caminho do ponto de extremidade do servidor for um ponto de montagem de volume.
    
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
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis. Para saber mais, confira [Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure, o cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivo do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Os arquivos podem falhar na camada se pagefile.sys estiver localizado em um volume que tenha a camada de nuvem habilitada. O pagefile.sys deve estar localizado em um volume que tenha a camada de nuvem desabilitada.
