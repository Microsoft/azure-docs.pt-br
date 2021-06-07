---
title: Como implantar o serviço de gerenciamento de certificados do cofre OPC-Azure | Microsoft Docs
description: Como implantar o serviço de gerenciamento de certificados do cofre OPC do zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 98dc9345d2c8b392fd094458b612857d6d454739
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645995"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Compilar e implantar o serviço de gerenciamento de certificados do compartimento OPC

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como implantar o serviço de gerenciamento de certificados do Vault do OPC no Azure.

> [!NOTE]
> Para obter mais informações, consulte o [repositório do cofre OPC](https://github.com/Azure/azure-iiot-opc-vault-service)do github.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-required-software"></a>Instalar o software necessário

Atualmente, a operação de compilação e implantação é limitada ao Windows.
Os exemplos são todos escritos para .NET Standard C#, que você precisa para criar o serviço e os exemplos para implantação.
Todas as ferramentas de que você precisa para .NET Standard vêm com as ferramentas do .NET Core. Consulte [introdução ao .NET Core](/dotnet/articles/core/getting-started).

1. [Instale o .NET Core 2.1 +][dotnet-install].
2. [Instale o Docker][docker-url] (opcional, somente se o Build do Docker local for necessário).
4. Instale as [ferramentas de linha de comando do Azure para PowerShell][powershell-install].
5. Inscreva-se para uma [assinatura do Azure][azure-free].

### <a name="clone-the-repository"></a>Clonar o repositório

Se você ainda não fez isso, clone este repositório GitHub. Abra um prompt de comando ou terminal e execute o seguinte:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Como alternativa, você pode clonar o repositório diretamente no Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Criar e implantar o serviço do Azure no Windows

Um script do PowerShell fornece uma maneira fácil de implantar o microserviço do cofre OPC e o aplicativo.

1. Abra uma janela do PowerShell na raiz do repositório. 
3. Vá para a pasta implantar `cd deploy` .
3. Escolha um nome `myResourceGroup` que seja improvável de causar um conflito com outras páginas da Web implantadas. Consulte a seção "nome do site já em uso" mais adiante neste artigo.
5. Inicie a implantação com `.\deploy.ps1` para instalação interativa ou insira uma linha de comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se você planeja desenvolver com essa implantação, adicione `-development 1` para habilitar a interface do usuário do Swagger e para implantar compilações de depuração.
6. Siga as instruções no script para entrar em sua assinatura e fornecer informações adicionais.
9. Após uma operação de compilação e implantação bem-sucedida, você deverá ver a seguinte mensagem:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Em caso de problemas, consulte a seção "Solucionando problemas de falhas de implantação" mais adiante neste artigo.

8. Abra seu navegador favorito e abra a página do aplicativo: `https://myResourceGroup.azurewebsites.net`
8. Dê ao aplicativo Web e ao microserviço do compartimento OPC alguns minutos para fazer o aquecimento após a implantação. O home page da Web pode parar de responder no primeiro uso, por até um minuto, até que você obtenha as primeiras respostas.
11. Para dar uma olhada na API do Swagger, abra: `https://myResourceGroup-service.azurewebsites.net`
13. Para iniciar um servidor GDS local com dotnet, inicie `.\myResourceGroup-gds.cmd` . Com o Docker, inicie `.\myResourceGroup-dockergds.cmd` .

É possível reimplantar uma compilação com exatamente as mesmas configurações. Lembre-se de que essa operação renova todos os segredos do aplicativo e pode redefinir algumas configurações nos registros do aplicativo Azure Active Directory (AD do Azure).

Também é possível reimplantar apenas os binários do aplicativo Web. Com o parâmetro `-onlyBuild 1` , novos pacotes zip do serviço e o aplicativo são implantados nos aplicativos Web.

Após a implantação bem-sucedida, você pode começar a usar os serviços. Consulte [gerenciar o serviço de gerenciamento de certificados do cofre do OPC](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Excluir os serviços da assinatura

Faça assim:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Vá para o grupo de recursos no qual o serviço foi implantado.
3. Selecione **Excluir grupo de recursos** e confirme.
4. Após alguns instantes, todos os componentes de serviço implantados são excluídos.
5. Acesse **Azure Active Directory** > **Registros de aplicativo**.
6. Deve haver três registros listados para cada grupo de recursos implantado. Os registros têm os seguintes nomes: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Exclua cada registro separadamente.

Agora todos os componentes implantados são removidos.

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Use um nome de grupo de recursos curto e simples. O nome também é usado para nomear recursos e o prefixo de URL de serviço. Como tal, ele deve estar em conformidade com os requisitos de nomenclatura de recursos.  

### <a name="website-name-already-in-use"></a>O nome do site já está em uso

É possível que o nome do site já esteja em uso. Você precisa usar um nome de grupo de recursos diferente. Os nomes de host em uso pelo script de implantação são: https: \/ /resourcegroupname.azurewebsites.net e https: \/ /resourgroupname-Service.azurewebsites.net.
Outros nomes de serviços são criados pela combinação de hashes de nome curtos e são pouco prováveis de entrar em conflito com outros serviços.

### <a name="azure-ad-registration"></a>Registro do Azure AD 

O script de implantação tenta registrar três aplicativos do Azure AD no Azure AD. Dependendo de suas permissões no locatário do Azure AD selecionado, essa operação poderá falhar. Há duas opções:

- Se você escolher um locatário do Azure AD de uma lista de locatários, reinicie o script e escolha um diferente na lista.
- Como alternativa, implante um locatário privado do Azure AD em outra assinatura. Reinicie o script e selecione para usá-lo.

## <a name="deployment-script-options"></a>Opções de script de implantação

O script usa os seguintes parâmetros:


```
-resourceGroupName
```

Pode ser o nome de um grupo de recursos existente ou um novo.

```
-subscriptionId
```


Esta é a ID da assinatura na qual os recursos serão implantados. É opcional.

```
-subscriptionName
```


Como alternativa, você pode usar o nome da assinatura.

```
-resourceGroupLocation
```


Este é um local do grupo de recursos. Se especificado, esse parâmetro tentará criar um novo grupo de recursos neste local. Esse parâmetro também é opcional.


```
-tenantId
```


Este é o locatário do Azure AD a ser usado. 

```
-development 0|1
```

Isso é para implantar para desenvolvimento. Use Debug Build e defina o ambiente ASP.NET para desenvolvimento. Crie `.publishsettings` para importação no Visual Studio 2017, para permitir que ele implante o aplicativo e o serviço diretamente. Esse parâmetro também é opcional.

```
-onlyBuild 0|1
```

Isso é para recompilar e reimplantar apenas os aplicativos Web e para recriar os contêineres do Docker. Esse parâmetro também é opcional.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a implantar o cofre OPC do zero, você pode:

> [!div class="nextstepaction"]
> [Gerenciar cofre OPC](howto-opc-vault-manage.md)