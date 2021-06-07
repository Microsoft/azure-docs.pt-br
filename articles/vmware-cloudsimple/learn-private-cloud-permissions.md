---
title: Solução do Azure VMware por CloudSimple – modelo de permissão de nuvem privada
description: Descreve o modelo, os grupos e as categorias de permissão da nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1c8cfeda008955006f2fbad1df58c8047bd36541
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898038"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>Modelo de permissão de nuvem privada do CloudSimple do VMware vCenter

O CloudSimple mantém o acesso administrativo total ao ambiente de nuvem privada. Cada cliente do CloudSimple recebe privilégios administrativos suficientes para poder implantar e gerenciar as máquinas virtuais em seu ambiente.  Se necessário, você pode escalonar seus privilégios temporariamente para executar funções administrativas.

## <a name="cloud-owner"></a>Proprietário da nuvem

Quando você cria uma nuvem privada, um usuário **CloudOwner** é criado no vCenter Sign-On domínio único, com acesso de **função de proprietário de nuvem** para gerenciar objetos na nuvem privada. Esse usuário também pode configurar fontes de [identidade](set-vcenter-identity.md)adicionais do vCenter e outros usuários para a nuvem privada do vCenter.

> [!NOTE]
> O usuário padrão para seu vCenter de nuvem privada do CloudSimple é cloudowner@cloudsimple.local quando uma nuvem privada é criada.

## <a name="user-groups"></a>Grupos de usuários

Um grupo chamado **Cloud-Owner-Group** é criado durante a implantação de uma nuvem privada. Os usuários nesse grupo podem administrar várias partes do ambiente vSphere na nuvem privada. Esse grupo recebe automaticamente os privilégios de  **função de proprietário da nuvem** e o usuário  **CloudOwner** é adicionado como um membro desse grupo.  O CloudSimple cria grupos adicionais com privilégios limitados para facilitar o gerenciamento.  Você pode adicionar qualquer usuário a esses grupos criados previamente e os privilégios definidos abaixo são automaticamente atribuídos aos usuários nos grupos.

### <a name="pre-created-groups"></a>Grupos pré-criados

| Nome do Grupo | Finalidade | Função |
| -------- | ------- | ------ |
| Grupo de proprietário da nuvem | Os membros deste grupo têm privilégios administrativos para a nuvem privada vCenter | [Nuvem-proprietário-função](#cloud-owner-role) |
| Cloud-global-cluster-admin-Group | Os membros deste grupo têm privilégios administrativos no cluster do vCenter de nuvem privada | [Cloud-cluster-admin-role](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-admin-Group | Os membros deste grupo podem gerenciar o armazenamento na nuvem privada vCenter | [Cloud-Storage-admin-role](#cloud-storage-admin-role) |
| Cloud-Global-Network-admin-Group | Os membros deste grupo podem gerenciar a rede e os grupos de portas distribuídas na nuvem privada vCenter | [Cloud-Network-admin-role](#cloud-network-admin-role) |
| Cloud-global-VM-admin-Group | Os membros deste grupo podem gerenciar máquinas virtuais na nuvem privada vCenter | [Cloud-VM-admin-role](#cloud-vm-admin-role) |

Para conceder permissões a usuários individuais para gerenciar a nuvem privada, crie contas de usuário adicionar aos grupos apropriados.

> [!CAUTION]
> Novos usuários devem ser adicionados somente a *Cloud-Owner-Group*, *Cloud-global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* ou, *Cloud-global-VM-admin-Group*.  Os usuários adicionados ao grupo de *Administradores* serão removidos automaticamente.  Somente as contas de serviço devem ser adicionadas ao grupo de *Administradores* e as contas de serviço não devem ser usadas para entrar na interface do usuário da Web do amvSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista de privilégios do vCenter para funções padrão

### <a name="cloud-owner-role"></a>Nuvem-proprietário-função

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Alarmes** | Reconhecer alerta <br> Criar alarme <br> Desabilitar ação de alarme <br> Modificar alarme <br> Remover alarme <br> Definir status do alarme |
| **Permissões** | Modificar permissão |
| **Biblioteca de conteúdo** | Adicionar item de biblioteca <br> Criar biblioteca local <br> Criar biblioteca inscrita <br> Excluir item de biblioteca <br> Excluir biblioteca local <br> Excluir biblioteca assinada <br> Baixar arquivos <br> Remover item de biblioteca <br> Remover biblioteca assinada <br> Importar armazenamento <br> Informações de assinatura de investigação <br> Armazenamento de leitura <br> Sincronizar item de biblioteca <br> Sincronizar biblioteca assinada <br> Introspecção de tipo <br> Atualizar definições de configuração <br> Arquivos de atualização <br> Atualizar biblioteca <br> Atualizar item de biblioteca <br> Atualizar biblioteca local <br> Atualizar biblioteca assinada <br> Exibir definições de configuração |
| **Operações criptográficas** | Adicionar disco <br> Clone <br> Descriptografar <br> Direct Access <br> Encrypt <br> Criptografar novo <br> Gerenciar KMS <br> Gerenciar políticas de criptografia <br> Gerenciar chaves <br> Migrações <br> Recriptografado <br> Registrar VM <br> Registrar host |
| **grupo de dvPort** | Criar <br> Excluir <br> Modificar <br> Operação de política <br> Operação de escopo |
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados <br> Configurar repositório de armazenamento <br> Operações de arquivo de nível baixo <br> Mover repositório de armazenamento <br> Remover repositório de armazenamento <br> Remover arquivo <br> Renomear repositório de armazenamento <br> Atualizar arquivos de máquina virtual <br> Atualizar metadados da máquina virtual |
| **Gerenciador de Agentes ESX** | Config <br> Modificar <br> Visualizar |
| **Extensão** | Registrar extensão <br> Cancelar registro da extensão <br> Atualizar extensão |
| **Provedor de estatísticas externas**| Registrar <br> Cancelar o registro  <br> Atualizar |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Renomear pasta |
| **Global** | Cancelar tarefa <br> planejamento de capacidade <br> Diagnósticos <br> Desabilitar métodos <br> Habilitar métodos <br> Marca global <br> Saúde <br> Licenças <br> Evento de log <br> Gerenciar atributos personalizados <br> Proxy <br> Ação de script <br> Gerenciadores de serviços <br> Definir atributo personalizado <br> Marca do sistema |
| **Provedor de atualização de integridade** | Registrar <br> Cancelar o registro  <br> Atualizar |
| **Configuração de > de host** | Configuração da partição de armazenamento |
| **Inventário de > de host** | Modificar cluster |
| **Marcação de vSphere** | Atribuir ou cancelar a atribuição de marca vSphere <br> Criar marca vSphere <br> Criar categoria de marca vSphere <br> Excluir marca vSphere <br> Excluir categoria de marca vSphere <br> Editar marca vSphere <br> Editar categoria da marca vSphere <br> Modificar o campo UsedBy para a categoria <br> Modificar campo UsedBy para marca |
| **Rede** | Assign network <br> Configurar <br> Mover rede <br> Remover |
| **Desempenho** | Modificar intervalos |
| **Perfil de host** | Visualizar |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Assign virtual machine to resource pool <br> Criar pool de recursos <br> Migrar máquina virtual desligada <br> Migrar máquina virtual ligada <br> Modificar pool de recursos <br> Mover pool de recursos <br> Consultar o vMotion <br> Remover pool de recursos <br> Renomear pool de recursos |
| **Tarefa agendada** | Criar tarefas <br> Modificar tarefa <br> Remover tarefa <br> Executar tarefa |
| **Sessões** | Representar usuário <br> Mensagem <br> Validar sessão <br> Exibir e parar sessões |
| **Cluster de repositório de armazenamento** | Configurar um cluster de repositório de armazenamento |
| **Armazenamento controlado por perfil** | Atualização de armazenamento controlada por perfil <br> Exibição de armazenamento controlada por perfil |
| **Exibições de armazenamento** | Configurar serviços <br> Visualizar |
| **Tarefas** | Criar tarefa <br> Atualizar tarefa |
| **Serviço de transferência**| Gerenciar <br> Monitoramento |
| **vApp** | Adicionar máquina virtual <br> Atribuir pool de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Excluir <br> Exportação <br> Importar <br> Mover <br> Desligar <br> Ligue <br> Renomear <br> Suspend <br> Cancelar o registro  <br> Exibir ambiente OVF <br> configuração do aplicativo vApp <br> configuração da instância de vApp <br> configuração do vApp managedBy <br> configuração do recurso vApp |
| **VRMPolicy** | VRMPolicy de consulta <br> Atualizar VRMPolicy |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar o managedBy <br> Controle de alterações do disco <br> Concessão de disco <br> Exibir configurações de conexão <br> Estender disco virtual <br> Dispositivo USB do host <br> Memória <br> Modificar as configurações de dispositivo <br> Compatibilidade de tolerância a falhas de consulta <br> Consultar arquivos sem proprietário <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Renomear <br> Redefinir informações de convidado <br> Definir anotação <br> Configurações <br> Posicionamento do Swapfile <br> Alternar bifurcação pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Operações de convidado de > de máquina virtual** | Modificação do alias da operação de convidado <br> Consulta de alias de operação de convidado <br> Modificações de operação de convidado <br> Execução do programa de operação de convidado <br> Consultas de operação de convidado |
| **Interação de > de máquina virtual** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configurar mídia de CD <br> Configurar mídia de disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão de dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela API do VIX <br> Injetar códigos de verificação de HID USB <br> Pausar ou pausar <br> Executar operações de apagamento ou redução <br> Desligar <br> Ligue <br> Registrar sessão na máquina virtual <br> Repetir sessão na máquina virtual <br> Redefinir <br> Retomar tolerância a falhas <br> Suspend <br> Tolerância a falhas de suspensão <br> Failover de Teste <br> VM secundária de reinício de teste <br> Desativar tolerância a falhas <br> Ativar tolerância a falhas <br> Instalação das ferramentas do VMware |
| **Inventário de > de máquina virtual** | Criar a partir de existente <br> Criar <br> Mover <br> Registrar <br> Remover <br> Cancelar o registro  |
| **Provisionamento de > de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Allow read-only disk access <br> Permitir download de máquina virtual <br> Permitir carregamento de arquivos de máquina virtual <br> Clonar modelo <br> Clonar máquina virtual <br> Criar modelo a partir da máquina virtual <br> Personalizar <br> Implantar modelo <br> Marcar como modelo <br> Marcar como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Ler especificações de personalização |
| **Configuração do serviço de > de máquina virtual** | Permitir notificações <br> Permitir sondagem de notificações de eventos globais <br> Gerenciar configurações de serviço <br> Modificar configuração de serviço <br> Configurações do serviço de consulta <br> Ler configuração de serviço |
| **Gerenciamento de instantâneos de > de máquina virtual** | Create snapshot <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação vSphere de máquina virtual >** | Configurar replicação <br> Gerenciar a replicação <br> Monitorar a replicação |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Atualizar dependência |

### <a name="cloud-cluster-admin-role"></a>Cloud-cluster-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados <br> Configurar repositório de armazenamento <br> Operações de arquivo de nível baixo <br> Remover repositório de armazenamento <br> Renomear repositório de armazenamento <br> Atualizar arquivos de máquina virtual <br> Atualizar metadados da máquina virtual |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Renomear pasta |
| **Configuração de > de host**  | Configuração da partição de armazenamento |
| **Marcação de vSphere** | Atribuir ou cancelar a atribuição de marca vSphere <br> Criar marca vSphere <br> Criar categoria de marca vSphere <br> Excluir marca vSphere <br> Excluir categoria de marca vSphere <br> Editar marca vSphere <br> Editar categoria da marca vSphere <br> Modificar o campo UsedBy para a categoria <br> Modificar campo UsedBy para marca |
| **Rede** | Assign network |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Assign virtual machine to resource pool <br> Criar pool de recursos <br> Migrar máquina virtual desligada <br> Migrar máquina virtual ligada <br> Modificar pool de recursos <br> Mover pool de recursos <br> Consultar o vMotion <br> Remover pool de recursos <br> Renomear pool de recursos |
| **vApp** | Adicionar máquina virtual <br> Atribuir pool de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Excluir <br> Exportação <br> Importar <br> Mover <br> Desligar <br> Ligue <br> Renomear <br> Suspend <br> Cancelar o registro  <br> Exibir ambiente OVF <br> configuração do aplicativo vApp <br> configuração da instância de vApp <br> configuração do vApp managedBy <br> configuração do recurso vApp |
| **VRMPolicy** | VRMPolicy de consulta <br> Atualizar VRMPolicy |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar o managedBy <br> Controle de alterações do disco <br> Concessão de disco <br> Exibir configurações de conexão <br> Estender disco virtual <br> Dispositivo USB do host <br> Memória <br> Modificar as configurações de dispositivo <br> Compatibilidade de tolerância a falhas de consulta <br> Consultar arquivos sem proprietário <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Renomear <br> Redefinir informações de convidado <br> Definir anotação <br> Configurações <br> Posicionamento do Swapfile <br> Alternar bifurcação pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Operações de convidado de > de máquina virtual** | Modificação do alias da operação de convidado <br> Consulta de alias de operação de convidado <br> Modificações de operação de convidado <br> Execução do programa de operação de convidado <br> Consultas de operação de convidado |
| **Interação de > de máquina virtual** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configurar mídia de CD <br> Configurar mídia de disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão de dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela API do VIX <br> Injetar códigos de verificação de HID USB <br> Pausar ou pausar <br> Executar operações de apagamento ou redução <br> Desligar <br> Ligue <br> Registrar sessão na máquina virtual <br> Repetir sessão na máquina virtual <br> Redefinir <br> Retomar tolerância a falhas <br> Suspend <br> Tolerância a falhas de suspensão <br> Failover de Teste <br> VM secundária de reinício de teste <br> Desativar tolerância a falhas <br> Ativar tolerância a falhas <br> Instalação das ferramentas do VMware
| **Inventário de > de máquina virtual** | Criar a partir de existente <br> Criar <br> Mover <br> Registrar <br> Remover <br> Cancelar o registro  |
| **Provisionamento de > de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Allow read-only disk access <br> Permitir download de máquina virtual <br> Permitir carregamento de arquivos de máquina virtual <br> Clonar modelo <br> Clonar máquina virtual <br> Criar modelo a partir da máquina virtual <br> Personalizar <br> Implantar modelo <br> Marcar como modelo <br> Marcar como máquina virtual <br> Modificar especificação de personalização <br> Promover discos  <br> Ler especificações de personalização |
| **Configuração do serviço de > de máquina virtual** | Permitir notificações <br> Permitir sondagem de notificações de eventos globais <br> Gerenciar configurações de serviço <br> Modificar configuração de serviço <br> Configurações do serviço de consulta <br> Ler configuração de serviço
| **Gerenciamento de instantâneos de > de máquina virtual** | Create snapshot <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação vSphere de máquina virtual >** | Configurar replicação <br> Gerenciar a replicação <br> Monitorar a replicação |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Atualizar dependência |

### <a name="cloud-storage-admin-role"></a>Cloud-Storage-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados <br> Configurar repositório de armazenamento <br> Operações de arquivo de nível baixo <br> Remover repositório de armazenamento <br> Renomear repositório de armazenamento <br> Atualizar arquivos de máquina virtual <br> Atualizar metadados da máquina virtual |
| **Configuração de > de host** | Configuração da partição de armazenamento |
| **Cluster de repositório de armazenamento** | Configurar um cluster de repositório de armazenamento |
| **Armazenamento controlado por perfil** | Atualização de armazenamento controlada por perfil <br> Exibição de armazenamento controlada por perfil |
| **Exibições de armazenamento** | Configurar serviços <br> Visualizar |

### <a name="cloud-network-admin-role"></a>Cloud-Network-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **grupo de dvPort** | Criar <br> Excluir <br> Modificar <br> Operação de política <br> Operação de escopo |
| **Rede** | Assign network <br> Configurar <br> Mover rede <br> Remover |
| **Configuração de > de máquina virtual** | Modificar as configurações de dispositivo |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Datastore** | Alocar espaço <br> Procurar no Repositório de Dados |
| **Rede** | Assign network |
| **Recurso** | Assign virtual machine to resource pool <br> Migrar máquina virtual desligada <br> Migrar máquina virtual ligada
| **vApp** | Exportação <br> Importar |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar o managedBy <br> Controle de alterações do disco <br> Concessão de disco <br> Exibir configurações de conexão <br> Estender disco virtual <br> Dispositivo USB do host <br> Memória <br> Modificar as configurações de dispositivo <br> Compatibilidade de tolerância a falhas de consulta <br> Consultar arquivos sem proprietário <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Renomear <br> Redefinir informações de convidado <br> Definir anotação <br> Configurações <br> Posicionamento do Swapfile <br> Alternar bifurcação pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Operações de convidado de >de máquina virtual** | Modificação do alias da operação de convidado <br> Consulta de alias de operação de convidado <br> Modificações de operação de convidado <br> Execução do programa de operação de convidado <br> Consultas de operação de convidado    |
| **Interação de >de máquina virtual** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configurar mídia de CD <br> Configurar mídia de disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão de dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela API do VIX <br> Injetar códigos de verificação de HID USB <br> Pausar ou pausar <br> Executar operações de apagamento ou redução <br> Desligar <br> Ligue <br> Registrar sessão na máquina virtual <br> Repetir sessão na máquina virtual <br> Redefinir <br> Retomar tolerância a falhas <br> Suspend <br> Tolerância a falhas de suspensão <br> Failover de Teste <br> VM secundária de reinício de teste <br> Desativar tolerância a falhas <br> Ativar tolerância a falhas <br> Instalação das ferramentas do VMware |
| **Inventário de >de máquina virtual** | Criar a partir de existente <br> Criar <br> Mover <br> Registrar <br> Remover <br> Cancelar o registro  |
| **Provisionamento de >de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Allow read-only disk access <br> Permitir download de máquina virtual <br> Permitir carregamento de arquivos de máquina virtual <br> Clonar modelo <br> Clonar máquina virtual <br> Criar modelo a partir da máquina virtual <br> Personalizar <br> Implantar modelo <br> Marcar como modelo <br> Marcar como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Ler especificações de personalização |
| **Configuração do serviço de >de máquina virtual** | Permitir notificações <br> Permitir sondagem de notificações de eventos globais <br> Gerenciar configurações de serviço <br> Modificar configuração de serviço <br> Configurações do serviço de consulta <br> Ler configuração de serviço
| **Gerenciamento de instantâneos de >de máquina virtual** | Create snapshot <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação vSphere de máquina virtual >** | Configurar replicação <br> Gerenciar a replicação <br> Monitorar a replicação |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Atualizar dependência |
