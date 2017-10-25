---
title: "Como usar a API de gerenciamento de serviços (Python) - guia de recursos"
description: "Saiba como executar tarefas de gerenciamento de serviços comuns de forma programática no Python."
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 13249ba9a4b317a3154776b411ce0bb1f316b3bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-management-from-python"></a>Como usar o Gerenciamento de Serviços no Python
Este guia mostra como executar tarefas de gerenciamento de serviços comuns de forma programática no Python. A classe **ServiceManagementService** no [SDK do Azure para Python](https://github.com/Azure/azure-sdk-for-python) dá suporte a acesso programático para grande parte da funcionalidade relacionada ao gerenciamento de serviços que está disponível no [Portal Clássico do Azure][management-portal] (como **criação, atualização e exclusão de serviços de nuvem, implantações, serviços de gerenciamento de dados e máquinas virtuais**). Essa funcionalidade pode ser útil na criação de aplicativos que precisam de acesso programático ao gerenciamento de serviços.

## <a name="WhatIs"> </a>O que é gerenciamento de serviços
A API de Gerenciamento de Serviços fornece acesso programático à grande parte da funcionalidade do gerenciamento de serviços disponível por meio do [Portal Clássico do Azure][management-portal]. O SDK do Azure para Python permite que você gerencie os serviços de nuvem e as contas de armazenamento.

Para usar a API de Gerenciamento de Serviços, é necessário [criar uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Conceitos
O SDK do Azure para Python encapsula a [API de Gerenciamento de Serviços do Azure][svc-mgmt-rest-api], que é uma API REST. Todas as operações da API são executadas por meio do SSL e mutuamente autenticadas usando certificados X.509 v3. O serviço de gerenciamento pode ser acessado em um serviço em execução no Azure ou diretamente pela Internet em qualquer aplicativo que possa enviar uma solicitação HTTPS e receber uma resposta HTTPS.

## <a name="Installation"> </a>Instalação
Todos os recursos descritos neste artigo estão disponíveis no pacote do `azure-servicemanagement-legacy` , que pode ser instalado usando o pip. Para obter mais informações sobre a instalação (por exemplo, se você for novo no Python), consulte este artigo: [Instalando o Python e o SDK do Azure](../python-how-to-install.md)

## <a name="Connect"> </a>Como conectar-se ao gerenciamento de serviços
Para conectar-se ao ponto de extremidade do Gerenciamento de Serviços, você precisa da ID de sua assinatura do Azure e um certificado de gerenciamento válido. Você pode obter sua ID de assinatura por meio do [Portal Clássico do Azure][management-portal].

> [!NOTE]
> Agora é possível usar certificados criados com o OpenSSL quando for executado no Windows.  Isso requer o Python 2.7.4 ou posterior. Recomendamos que os usuários usem o OpenSSL, em vez de .pfx, já que o suporte para certificados .pfx provavelmente será removido no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gerenciamento no Windows/Mac/Linux (OpenSSL)
Você também pode usar o [OpenSSL](http://www.openssl.org/) para criar o certificado de gerenciamento.  Na verdade, você precisa criar dois certificados, um para o servidor (um arquivo `.cer`) e um para o cliente (um arquivo `.pem`). Para criar o arquivo `.pem` , execute isto:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar o certificado `.cer` , execute isto:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para saber mais sobre certificados do Azure, confira [Visão geral dos Certificados de Serviços de Nuvem do Azure](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros do OpenSSL, consulte a documentação em [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Depois de criar esses arquivos, você precisará carregar o arquivo `.cer` no Azure por meio da ação "Carregar" da guia "Configurações" do [Portal Clássico do Azure][management-portal] e anotar o local no qual você salvou o arquivo `.pem`.

Depois de ter obtido a ID de sua assinatura, criado um certificado e carregado o arquivo `.cer` no Azure, você pode conectar-se ao ponto de extremidade de gerenciamento do Azure passando a ID da assinatura e o caminho para o arquivo `.pem` para o **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo acima, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gerenciamento no Windows (MakeCert)
Você pode criar um certificado de gerenciamento autoassinado em seu computador usando `makecert.exe`.  Abra um **prompt de comando do Visual Studio** como **administrador** e use o seguinte comando, substituindo *AzureCertificate* pelo nome do certificado que você deseja usar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando cria o arquivo `.cer` e o instala no repositório de certificados **Pessoal** . Para obter mais informações, confira a [Visão geral sobre certificados para os Serviços de Nuvem do Azure](cloud-services-certs-create.md).

Depois que tiver criado o certificado, você precisará carregar do arquivo `.cer` no Azure por meio da ação "Carregar" da guia "Configurações" no [Portal Clássico do Azure][management-portal].

Depois que tiver obtido a ID da sua assinatura, criado um certificado e carregado o arquivo `.cer` para o Azure, você poderá conectar-se ao ponto de extremidade de gerenciamento do Azure, passando a ID da assinatura e a localização do certificado em seu repositório de certificados **Pessoal** para **ServiceManagementService** (novamente, substitua *AzureCertificate* pelo nome do seu certificado):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo acima, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

## <a name="ListAvailableLocations"> </a>Como listar os locais disponíveis
Para listar os locais que estão disponíveis para hospedar serviços, use o método **list\_locations**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Ao criar um serviço de nuvem ou de armazenamento, é preciso fornecer uma localização válida. O método **list\_locations** sempre retorna uma lista atualizada dos locais disponíveis no momento. Na data da criação deste artigo, os locais disponíveis são:

* Europa Ocidental
* Norte da Europa
* Sudeste Asiático
* Ásia Oriental
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

## <a name="CreateCloudService"> </a>Como criar um serviço de nuvem
Quando você cria um aplicativo e o executa no Azure, o código e a configuração, juntos, são chamados de [serviço de nuvem][cloud service] do Azure (conhecido como *serviço hospedado* em versões anteriores do Azure). O método **create\_hosted\_service** permite que você crie um novo serviço hospedado, fornecendo um nome de serviço hospedado (que deve ser exclusivo no Azure), um rótulo (automaticamente codificado em base64), uma descrição e uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Você pode listar todos os serviços hospedados para seu assinatura com o método **list\_hosted\_services**:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Se desejar obter informações sobre um determinado serviço hospedado, você pode fazê-lo passando o nome do serviço hospedado para o método **get\_hosted\_service\_properties**:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Depois de criar um serviço de nuvem, você pode implantar seu código de serviço com o método **create\_deployment**.

## <a name="DeleteCloudService"> </a>Como excluir um serviço de nuvem
Você pode excluir um serviço de nuvem passando o nome do serviço para o método **delete\_hosted\_service**:

    sms.delete_hosted_service('myhostedservice')

Para que seja possível excluir um serviço, todas as implantações do serviço devem ser excluídas primeiro. (Consulte [Como excluir uma implantação](#DeleteDeployment) para obter detalhes.)

## <a name="DeleteDeployment"> </a>Como excluir uma implantação
Para excluir uma implantação, use o método **delete\_deployment**. O exemplo a seguir mostra como excluir uma implantação chamada `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Como criar um serviço de armazenamento
Um [serviço de armazenamento](../storage/common/storage-create-storage-account.md) fornece acesso a [Blobs](../storage/blobs/storage-python-how-to-use-blob-storage.md), [Tables](../cosmos-db/table-storage-how-to-use-python.md) e [Queues](../storage/queues/storage-python-how-to-use-queue-storage.md) do Azure. Para criar um serviço de armazenamento, você precisa de um nome para o serviço (com 3 a 24 caracteres minúsculos e exclusivo no Azure), uma descrição, um rótulo (até 100 caracteres, automaticamente codificado em base64) e um local. O exemplo a seguir mostra como criar um serviço de armazenamento especificando um local.

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

Observe no exemplo acima que o status da operação **create\_storage\_account** pode ser recuperada passando o resultado retornado pelo **create\_storage\_account** ao método **get\_operation\_status**.  

Você pode listar suas contas de armazenamento e suas propriedades com o método **list\_storage\_accounts**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Como excluir um serviço de armazenamento
Você pode excluir um serviço de armazenamento passando o nome do serviço para o método **delete\_storage\_account**. A exclusão de um serviço de armazenamento excluirá todos os dados armazenados no serviço (blobs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Como listar os sistemas operacionais disponíveis
Para listar os sistemas operacionais que estão disponíveis para hospedar serviços, use o método **list\_operating\_systems**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Como alternativa, você pode usar o método **list\_operating\_system\_families** que agrupa os sistemas operacionais por família:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Como criar uma imagem do sistema operacional
Para adicionar uma imagem do sistema operacional ao repositório de imagens, use o método **add\_os\_image**:

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

Para listar as imagens do sistema operacional estão disponíveis, use o método **list\_os\_images**. Isso inclui todas as imagens de plataforma e imagens do usuário:

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

## <a name="DeleteVMImage"> </a>Como excluir uma imagem do sistema operacional
Para excluir uma imagem de usuário, use o método **delete\_os\_image**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Como criar uma máquina virtual
Para criar uma máquina virtual, você precisa primeiro criar um [serviço de nuvem](#CreateCloudService).  Em seguida, criar a implantação da máquina virtual usando o método **create\_virtual\_machine\_deployment**:

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

## <a name="DeleteVM"> </a>Como excluir uma máquina virtual
Para excluir uma máquina virtual, primeiro você exclui a implantação usando o método **delete\_deployment**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço de nuvem pode ser excluído usando o método **delete\_hosted\_service**:

    sms.delete_hosted_service(service_name='myvm')

## <a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Como criar uma máquina virtual por meio de uma imagem de máquina virtual capturada
Para capturar uma imagem de VM, você primeiro chama o método **capture\_vm\_image**:

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

Em seguida, para certificar-se de que você capturou a imagem com sucesso, use a API **list\_vm\_images** e certifique-se de que a imagem seja exibida nos resultados:

    images = sms.list_vm_images()

Para criar finalmente a máquina virtual usando a imagem capturada, use o método **create\_virtual\_machine\_deployment** como antes, mas desta vez passe vm_image_name

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

Para saber mais sobre como capturar uma máquina Virtual Linux, consulte [Como capturar uma máquina Virtual Linux.](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

Para saber mais sobre como capturar uma máquina Virtual Windows, consulte [Como capturar uma máquina Virtual Windows.](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="What's Next"> </a>Próximas etapas
Agora que você aprendeu os fundamentos do gerenciamento de serviços, você pode acessar a [documentação de referência da API completa para o SDK do Azure Python](http://azure-sdk-for-python.readthedocs.org/) e realizar tarefas complexas facilmente para gerenciar seu aplicativo python.

Para saber mais, consulte o [Centro de Desenvolvedores do Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
