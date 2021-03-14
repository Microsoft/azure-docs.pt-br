---
title: Solucionar problemas de implantação da plataforma de contêiner do OpenShift 3,11 no Azure
description: Solucionar problemas de implantação da plataforma de contêiner do OpenShift 3,11 no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: b6dfb4cb697e7d66c7db323df9786ff7ad45e1d7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672330"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Solucionar problemas de implantação da plataforma de contêiner do OpenShift 3,11 no Azure

Se o cluster do OpenShift não for implantado com êxito, o portal do Azure fornecerá a saída de erro. A saída pode ser difícil de ler, o que dificulta a identificação do problema. Examine rapidamente essa saída para o código de saída 3, 4 ou 5. A seguir, informações sobre esses três códigos de saída:

- Código de Saída 3: Nome/senha de usuário de assinatura do Red Hat ou ID/chave de ativação estão incorretos
- Código de Saída 4: a ID do Pool do Red Hat está incorreta ou não há direitos disponíveis
- Código de Saída 5: não foi possível provisionar o Volume do pool dinâmico do Docker

Para todos os outros códigos de saída, conecte-se ao (s) host (s) via ssh para visualizar os arquivos de log.

**OpenShift Container Platform 3.11**

SSH para o host ansioso playbook. Para o modelo ou a oferta do Marketplace, use o host de bastiões. Do bastião, você pode SSH para todos os outros nós no cluster (mestre, infra, CNS, compute). Você precisará ser root para visualizar os arquivos de log. A raiz está desativada para o acesso SSH por padrão, portanto, não use root para SSH em outros nós.

**OKD**

SSH para o host ansioso playbook. Para o modelo OLD (versão 3.9 e anterior), use o host master-0. Para o modelo OLD (versão 3.10 e posterior), use o host de bastiões. Do ansible playbook host, você pode SSH para todos os outros nós no cluster (mestre, infra, CNS, compute). Você precisará ser root (sudo su -) para visualizar os arquivos de log. A raiz está desativada para o acesso SSH por padrão, portanto, não use root para SSH em outros nós.

## <a name="log-files"></a>Arquivos de log

Os arquivos de log (stderr e stdout) para os scripts de preparação do host estão localizados em `/var/lib/waagent/custom-script/download/0` em todos os hosts. Se ocorreu um erro durante a preparação do host, exiba esses arquivos de log para determinar o erro.

Se os scripts de preparação forem executados com êxito, os arquivos de log no `/var/lib/waagent/custom-script/download/1` diretório do host do guia estratégico Ansible precisarão ser examinados. Se o erro ocorreu durante a instalação real do OpenShift, o arquivo stdout exibirá o erro. Use estas informações para entrar em contato com o Suporte para obter mais assistência.

Saída de exemplo

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Os erros mais comuns durante a instalação são:

1. Chave privada tem a frase secreta
2. O segredo do cofre da chave com chave privada não foi criado corretamente
3. As credenciais do principal de serviço foram inseridas incorretamente
4. A entidade de serviço não tem acesso de contribuidor ao grupo de recursos

### <a name="private-key-has-a-passphrase"></a>Chave privada tem uma senha

Você verá um erro de que a permissão foi negada para SSH. SSH para o host do manual do Ansible para verificar uma frase secreta na chave privada.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>O segredo do cofre da chave com chave privada não foi criado corretamente

A chave privada é copiada para o host do guia estratégico Ansible-~/.ssh/id_rsa. Confirme se este arquivo está correto. Teste abrindo uma sessão SSH em um dos nós do cluster a partir do ansible host do manual.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>As credenciais do principal de serviço foram inseridas incorretamente

Ao fornecer a entrada para o modelo ou a oferta do Marketplace, as informações incorretas foram fornecidas. Certifique-se de usar o appId (clientId) e a senha (clientSecret) corretos para o principal do serviço. Verifique emitindo o seguinte comando azure cli.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>A entidade de serviço não tem acesso de contribuidor ao grupo de recursos

Se o provedor de nuvem do Azure estiver habilitado, o principal de serviço usado deverá ter acesso de colaborador ao grupo de recursos. Verifique emitindo o seguinte comando azure cli.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ferramentas adicionais

Para alguns erros, você também pode usar os seguintes comandos para obter mais informações:

1. status de systemctl \<service>
2. journalctl -xe
