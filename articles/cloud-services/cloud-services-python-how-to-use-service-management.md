---
title: Usar a API de Gerenciamento de Serviços (Python) - guia de recursos
description: Saiba como executar tarefas de gerenciamento de serviços comuns de forma programática no Python.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 02993f2b79e37e5e50c20c4ee07220bcbd36edb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741393"
---
# <a name="use-service-management-from-python"></a>Usar o gerenciamento de serviços do Python

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Este guia mostra como executar tarefas de gerenciamento de serviços comuns de forma programática no Python. A classe **ServiceManagementService** no [SDK do Azure para Python](https://github.com/Azure/azure-sdk-for-python) dá suporte a acesso programático para grande parte da funcionalidade relacionada ao gerenciamento de serviços que está disponível no [Portal do Azure][management-portal]. Você pode usar essa funcionalidade para criar, atualizar e excluir serviços de nuvem, implantações, serviços de gerenciamento de dados e máquinas virtuais. Essa funcionalidade pode ser útil na criação de aplicativos que precisam de acesso programático ao gerenciamento de serviços.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>O que é gerenciamento de serviços?
A API de Gerenciamento de Serviços do Azure fornece acesso programático à grande parte da funcionalidade do gerenciamento de serviços disponível por meio do [Portal do Azure][management-portal]. Você pode usar o SDK do Azure para Python para gerenciar os serviços de nuvem e as contas de armazenamento.

Para usar a API de Gerenciamento de Serviços, é necessário [criar uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Conceitos
O SDK do Azure para Python encapsula a [API de Gerenciamento de Serviços][svc-mgmt-rest-api], que é uma API REST. Todas as operações de API são executadas por TLS e mutuamente autenticadas usando certificados X. 509 v3. O serviço de gerenciamento pode ser acessado dentro de um serviço em execução no Azure. Ele também pode ser acessado diretamente pela Internet em qualquer aplicativo que possa enviar uma solicitação HTTPS e receber uma resposta HTTPS.

## <a name="installation"></a><a name="Installation"> </a>Instalação
Todos os recursos descritos neste artigo estão disponíveis no pacote do `azure-servicemanagement-legacy`, que pode ser instalado usando o pip. Para obter mais informações sobre a instalação (por exemplo, se você for novo no Python), consulte: [Instalar o Python e o SDK do Azure](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Conectar-se ao gerenciamento de serviços
Para conectar-se ao ponto de extremidade do gerenciamento de serviços, você precisa da ID de sua assinatura do Azure e um certificado de gerenciamento válido. Você pode obter sua ID de assinatura por meio do [Portal do Azure][management-portal].

> [!NOTE]
> É possível usar certificados criados com o OpenSSL quando for executado no Windows. O Python 2.7.4 ou posterior é obrigatório. Recomendamos que você use o OpenSSL, em vez de .pfx, já que o suporte para certificados .pfx provavelmente será removido no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gerenciamento no Windows/Mac/Linux (OpenSSL)
Você também pode usar o [OpenSSL](https://www.openssl.org/) para criar o certificado de gerenciamento. Você precisa criar dois certificados, um para o servidor (um arquivo `.cer`) e um para o cliente (um arquivo `.pem`). Para criar o arquivo `.pem` , execute isto:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Para criar o certificado `.cer` , execute isto:

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Para saber mais sobre certificados do Azure, confira [Visão geral dos Certificados de Serviços de Nuvem do Azure](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros do OpenSSL, consulte a documentação em [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) .

Depois de criar esses arquivos, carregue o arquivo `.cer` no Azure. No [portal do Azure][management-portal], na guia **Configurações**, selecione **Carregar**. Observe onde você salvou o arquivo `.pem`.

Depois de obter a ID de assinatura, crie um certificado e carregue o arquivo `.cer` no Azure, conecte-se ao ponto de extremidade de gerenciamento do Azure. Conecte-se passando a ID da assinatura e o caminho do arquivo `.pem` para **ServiceManagementService**.

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

No exemplo acima, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gerenciamento no Windows (MakeCert)
Você pode criar um certificado de gerenciamento autoassinado no computador usando `makecert.exe`. Abra um **prompt de comando do Visual Studio** como **administrador** e use o seguinte comando, substituindo *AzureCertificate* pelo nome do certificado que você deseja usar:

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

O comando cria o `.cer` arquivo e o instala no repositório de certificados **pessoal** . Para obter mais informações, confira a [Visão geral sobre certificados para os Serviços de Nuvem do Azure](cloud-services-certs-create.md).

Depois de criar o certificado, carregue o arquivo `.cer` no Azure. No [portal do Azure][management-portal], na guia **Configurações**, selecione **Carregar**.

Depois de obter a ID de assinatura, crie um certificado e carregue o arquivo `.cer` no Azure, conecte-se ao ponto de extremidade de gerenciamento do Azure. Conecte-se passando a ID da assinatura e o local do certificado no repositório de certificados **Pessoal** para **ServiceManagementService** (novamente, substitua *AzureCertificate* pelo nome do certificado).

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

No exemplo acima, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Listar locais disponíveis
Para listar os locais que estão disponíveis para hospedar serviços, use o método **list\_locations**.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

Ao criar um serviço de nuvem ou de armazenamento, é preciso fornecer uma localização válida. O método **list\_locations** sempre retorna uma lista atualizada dos locais disponíveis no momento. Na data da criação deste artigo, os locais disponíveis são:

* Europa Ocidental
* Norte da Europa
* Sudeste Asiático
* Leste da Ásia
* Centro dos EUA
* Centro-Norte dos EUA
* Centro-Sul dos Estados Unidos
* Oeste dos EUA
* Leste dos EUA
* Leste do Japão
* Oeste do Japão
* Sul do Brasil
* Leste da Austrália
* Sudeste da Austrália

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Criar um serviço de nuvem
Quando você cria um aplicativo e o executa no Azure, seu código e a configuração, em conjunto, são chamados de [serviço de nuvem][cloud service] do Azure. (Ele era conhecido como um *serviço hospedado* em versões anteriores do Azure.) Você pode usar o **método \_ criar \_ serviço hospedado** para criar um novo serviço hospedado. Crie o serviço hospedado, fornecendo um nome de serviço hospedado (que deve ser exclusivo no Azure), um rótulo (automaticamente codificado em base64), uma descrição e uma localização.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

Você pode listar todos os serviços hospedados para seu assinatura com o método **list\_hosted\_services**.

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

Para obter informações sobre um determinado serviço hospedado, passe o nome do serviço hospedado para o método **get\_hosted\_service\_properties**.

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

Depois de criar um serviço de nuvem, implante seu código de serviço com o método **create\_deployment**.

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Excluir um serviço de nuvem
Você pode excluir um serviço de nuvem passando o nome do serviço para o método **excluir \_ \_ serviço hospedado** .

```python
sms.delete_hosted_service('myhostedservice')
```

Para que seja possível excluir um serviço, todas as implantações do serviço devem ser excluídas primeiro. Para saber mais, consulte [Excluir uma implantação](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Excluir uma implantação
Para excluir uma implantação, use o método **delete\_deployment**. O exemplo a seguir mostra como excluir uma implantação chamada `v1`:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Criar um serviço de armazenamento
Um [serviço de armazenamento](../storage/common/storage-account-create.md) fornece acesso a [BLOBs](../storage/blobs/storage-quickstart-blobs-python.md), [tabelas](../cosmos-db/table-storage-how-to-use-python.md)e [filas](../storage/queues/storage-python-how-to-use-queue-storage.md)do Azure. Para criar um serviço de armazenamento, é necessário um nome para o serviço (entre 3 e 24 caracteres minúsculos e exclusivos dentro do Azure). Você também precisa de uma descrição, um rótulo (até 100 caracteres codificados automaticamente com base64) e um local. O exemplo a seguir mostra como criar um serviço de armazenamento especificando um local:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mystorageaccount'
label = 'mystorageaccount'
location = 'West US'
desc = 'My storage account description.'

result = sms.create_storage_account(name, desc, label, location=location)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

No exemplo acima que o status da operação **create\_storage\_account** pode ser recuperada passando o resultado retornado pelo **create\_storage\_account** ao método **get\_operation\_status**. 

Você pode listar suas contas de armazenamento e suas propriedades com o método **list\_storage\_accounts**.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Excluir um serviço de armazenamento
Para excluir um serviço de armazenamento, passe o nome do serviço para o método **delete\_storage\_account**. A exclusão de um serviço de armazenamento excluirá todos os dados armazenados no serviço (blobs, tabelas e filas).

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Listar sistemas operacionais disponíveis
Para listar os sistemas operacionais que estão disponíveis para hospedar serviços, use o método **list\_operating\_systems**.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

Como alternativa, você pode usar o método **list\_operating\_system\_families** que agrupa os sistemas operacionais por família.

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Criar uma imagem do sistema operacional
Para adicionar uma imagem do sistema operacional ao repositório de imagens, use o método **Add \_ os \_ Image** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mycentos'
label = 'mycentos'
os = 'Linux' # Linux or Windows
media_link = 'url_to_storage_blob_for_source_image_vhd'

result = sms.add_os_image(label, media_link, name, os)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

Para listar as imagens do sistema operacional estão disponíveis, use o método **list\_os\_images**. Isso inclui todas as imagens de plataforma e imagens do usuário.

```python
result = sms.list_os_images()

for image in result:
    print('Name: ' + image.name)
    print('Label: ' + image.label)
    print('OS: ' + image.os)
    print('Category: ' + image.category)
    print('Description: ' + image.description)
    print('Location: ' + image.location)
    print('Media link: ' + image.media_link)
    print('')
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Excluir uma imagem do sistema operacional
Para excluir uma imagem de usuário, use o método **delete\_os\_image**.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Criar uma máquina virtual
Para criar uma máquina virtual, você precisa primeiro criar um [serviço de nuvem](#CreateCloudService). Em seguida, criar a implantação da máquina virtual usando o método **create\_virtual\_machine\_deployment**.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

# Name of an os image as returned by list_os_images
image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

# Destination storage account container/blob where the VM disk
# will be created
media_link = 'url_to_target_storage_blob_for_vm_hd'

# Linux VM configuration, you can use WindowsConfigurationSet
# for a Windows VM instead
linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

os_hd = OSVirtualHardDisk(image_name, media_link)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=os_hd,
    role_size='Small')
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Excluir uma máquina virtual
Para excluir uma máquina virtual, primeiro você exclui a implantação usando o método **delete\_deployment**.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

O serviço de nuvem pode ser excluído usando o método **delete\_hosted\_service**.

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Criar uma máquina virtual por meio de uma imagem de máquina virtual capturada
Para capturar uma imagem de VM, primeiro você chama o método **Capture \_ VM \_ Image** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

# replace the below three parameters with actual values
hosted_service_name = 'hs1'
deployment_name = 'dep1'
vm_name = 'vm1'

image_name = vm_name + 'image'
image = CaptureRoleAsVMImage    ('Specialized',
    image_name,
    image_name + 'label',
    image_name + 'description',
    'english',
    'mygroup')

result = sms.capture_vm_image(
        hosted_service_name,
        deployment_name,
        vm_name,
        image
    )
```

Para garantir que você tenha capturado com êxito a imagem, use a API **list\_vm\_images**. Verifique se a imagem é exibida nos resultados.

```python
images = sms.list_vm_images()
```

Para criar finalmente a máquina virtual usando a imagem capturada, use o método **create\_virtual\_machine\_deployment** como antes, mas desta vez passe vm_image_name.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=None,
    role_size='Small',
    vm_image_name = image_name)
```

Para saber mais sobre como capturar uma máquina virtual Linux no modelo de implantação clássico, consulte [Capturar uma máquina virtual Linux](/previous-versions/azure/virtual-machines/linux/classic/capture-image-classic).

Para saber mais sobre como capturar uma máquina virtual Windows no modelo de implantação clássico, consulte [Capturar uma máquina virtual Windows](/previous-versions/azure/virtual-machines/windows/classic/capture-image-classic).

## <a name="next-steps"></a><a name="What's Next"> </a>Próximas etapas
Agora que você aprendeu os fundamentos do gerenciamento de serviços, é possível acessar a [documentação de referência da API completa para o SDK do Python do Azure](https://azure-sdk-for-python.readthedocs.org/) e executar tarefas complexas facilmente para gerenciar seu aplicativo Python.

Para saber mais, confira o [Centro de Desenvolvedores do Python](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: /previous-versions/azure/ee460799(v=azure.100)


[cloud service]:/azure/cloud-services/