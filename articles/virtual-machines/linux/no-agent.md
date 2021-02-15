---
title: Criar imagens do Linux sem um agente de provisionamento
description: Crie imagens do Linux generalizadas sem um agente de provisionamento no Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 1c9ac872587804adbd9e62a3dc3ef3daed9e0c25
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223044"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Criando imagens generalizadas sem um agente de provisionamento

Microsoft Azure fornece agentes de provisionamento para VMs do Linux na forma de [walinuxagent](https://github.com/Azure/WALinuxAgent) ou [Cloud-init](https://github.com/canonical/cloud-init) (recomendado). Mas pode haver um cenário em que você não queira usar nenhum desses aplicativos para o agente de provisionamento, como:

- Sua versão/distribuição do Linux não dá suporte ao agente Cloud-init/Linux.
- Você precisa que propriedades específicas da VM sejam definidas, como nome do host.

> [!NOTE] 
>
> Se você não precisar que nenhuma propriedade seja definida ou que qualquer forma de provisionamento aconteça, considere a criação de uma imagem especializada.

Este artigo mostra como você pode configurar sua imagem de VM para atender aos requisitos da plataforma Azure e definir o nome do host, sem instalar um agente de provisionamento.

## <a name="networking-and-reporting-ready"></a>Rede e relatórios prontos

Para que sua VM Linux se comunique com os componentes do Azure, você precisará de um cliente DHCP para recuperar um IP de host da rede virtual, bem como a resolução de DNS e o gerenciamento de rotas. A maioria dos distribuiçõess é fornecida com esses utilitários prontos para uso. As ferramentas que foram testadas no Azure por fornecedores de distribuição do Linux incluem `dhclient` , `network-manager` `systemd-networkd` e outras.

> [!NOTE]
>
> Atualmente, a criação de imagens generalizadas sem um agente de provisionamento dá suporte apenas a VMs habilitadas para DHCP.

Após a instalação e configuração da rede, você deve "reportar pronto". Isso dirá ao Azure que a VM foi provisionada com êxito.

> [!IMPORTANT]
>
> A falha ao relatar pronto para o Azure resultará na reinicialização da VM!

## <a name="demosample"></a>Demonstração/exemplo

Esta demonstração mostrará como você pode usar uma imagem existente do Marketplace (nesse caso, uma VM Debian Buster) e remover o agente do Linux (walinuxagent), mas também criar o processo mais básico para relatar ao Azure que a VM está "pronta".

### <a name="create-the-resource-group-and-base-vm"></a>Crie o grupo de recursos e a VM base:

```bash
$ az group create --location eastus --name demo1
```

Criar a VM base:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Remover o agente de provisionamento de imagem

Depois que a VM estiver Provisionando, você poderá SSH nela e remover o agente do Linux:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Adicionar o código necessário à VM

Além disso, dentro da VM, como removemos o agente Linux do Azure, precisamos fornecer um mecanismo para relatar prontamente. 

#### <a name="python-script"></a>Script do Python

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Etapas genéricas (sem usar o Python)

Se sua VM não tiver o Python instalado ou disponível, você poderá reproduzir programaticamente essa lógica de script acima com as seguintes etapas:

1. Recupere o `ContainerId` e `InstanceId` analisando a resposta do WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. Construa os seguintes dados XML, injetando o analisado `ContainerId` e `InstanceId` a partir da etapa acima:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Poste esses dados no WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automatizando a execução do código na primeira inicialização

Esta demonstração usa o sistema, que é o sistema de inicialização mais comum no moderno Linux distribuições. Portanto, a maneira mais fácil e nativa de garantir que esse mecanismo de relatório pronto seja executado no momento certo é criar uma unidade de serviço do sistema. Você pode adicionar o seguinte arquivo de unidade a `/etc/systemd/system` (Este exemplo nomeia o arquivo de unidade `azure-provisioning.service` ):

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Esse serviço em sistema faz três coisas para o provisionamento básico:

1. Relatórios prontos para o Azure (para indicar que ele foi fornecido com êxito).
1. Renomeia a VM com base no nome da VM fornecida pelo usuário ao extrair esses dados do [serviço de metadados de instância do Azure (IMDS)](./instance-metadata-service.md). **Observação** O IMDS também fornece outros [metadados de instância](./instance-metadata-service.md#access-azure-instance-metadata-service), como chaves públicas SSH, para que você possa definir mais do que o nome do host.
1. Desabilita a si mesmo para que ele só seja executado na primeira inicialização e não nas reinicializações subsequentes.

Com a unidade no sistema de arquivos, execute o seguinte para habilitá-lo:

```bash
$ sudo systemctl enable azure-provisioning.service
```

Agora a VM está pronta para ser generalizada e tem uma imagem criada a partir dela. 

#### <a name="completing-the-preparation-of-the-image"></a>Concluindo a preparação da imagem

De volta ao seu computador de desenvolvimento, execute o seguinte para preparar a criação da imagem da VM de base:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

E crie a imagem a partir desta VM:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Agora, estamos prontos para criar uma nova VM (ou várias VMs) a partir da imagem:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> É importante definir `--enable-agent` como `false` porque walinuxagent não existe nessa VM que será criada a partir da imagem.

Esta VM deve ser provisionada com êxito. Fazendo logon na VM de provisionamento novo, você deve ser capaz de ver a saída do serviço de sistema pronto para o relatório:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Suporte

Se você implementar seu próprio código/agente de provisionamento, terá o suporte desse código, o suporte da Microsoft investigará apenas os problemas relacionados às interfaces de provisionamento que não estão disponíveis. Estamos continuamente fazendo melhorias e alterações nessa área, portanto, você deve monitorar as alterações na Cloud-init e no agente Linux do Azure para provisionar alterações de API.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [provisionamento do Linux](provisioning.md).
