---
title: Criar a infraestrutura de um cluster na AWS
description: Neste tutorial, você aprenderá como configurar a infraestrutura AWS para executar um cluster do Service Fabric.
ms.topic: tutorial
ms.date: 05/11/2018
ms.custom: mvc
ms.openlocfilehash: c7a18b0dcdc04bdf66ac4b36ce7376ee018eb238
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842896"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: Criar infraestrutura AWS para hospedar um cluster do Service Fabric

Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte da abordagem “qualquer sistema operacional, qualquer nuvem” que está sendo adotada pelo Service Fabric. Nesta série de tutoriais, você cria um cluster autônomo hospedado em AWS e instala um aplicativo nele.

Este tutorial é a primeira parte de uma série. Neste artigo, você gerará os recursos AWS necessários para hospedar o cluster autônomo do Service Fabric. Em artigos futuros, você precisará instalar o conjunto autônomo do Service Fabric, além de um aplicativo de exemplo no seu cluster e, por fim, limpar seu cluster.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar um conjunto de instâncias de EC2
> * Modificar o grupo de segurança
> * Entrar em uma das instâncias
> * Preparar a instância do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma conta do AWS.  Caso ainda não tenha uma conta, vá até o [Console do AWS](https://aws.amazon.com/) para criar uma.

## <a name="create-ec2-instances"></a>Criar instâncias de EC2

Entre no Console do AWS > insira **EC2** na caixa de pesquisa > **Servidores Virtuais do EC2 na Nuvem**

![Pesquisa de console do AWS][aws-console]

Selecione **Iniciar Instância**, na próxima tela, escolha **Selecionar** ao lado de Microsoft Windows Server 2016 Base.

![Seleção de instância de EC2][aws-ec2instance]

Selecione **t2.medium**, depois **Próximo: Configurar detalhes da instância**; na tela seguinte, altere a quantidade de instâncias para `3`, depois selecione **Detalhes Avançados** para expandir a seção.

Para conectar as máquinas virtuais juntas no Service Fabric, as VMs que estão hospedando sua infraestrutura precisam ter as mesmas credenciais.  Há duas maneiras comuns de obter credenciais consistentes: uni-las todas no mesmo domínio ou definir a mesma senha de administrador em cada VM.  Para este tutorial, use um script de dados de usuário para definir que todas as instâncias de EC2 tenham a mesma senha.  Em um ambiente de produção, é mais seguro unir os hosts em um domínio do Windows.

Insira o seguinte script no campo de dados do usuário no console:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Depois de inserir o script do PowerShell, selecione **Revisar e Inicializar**

![Revisão e inicialização de EC2][aws-ec2configure2]

Na tela de revisão, selecione **Inicializar**.  Depois, altere a lista suspensa para **Prosseguir sem um par de chaves** e selecione a caixa de seleção que indica que você sabe a senha.

![Seleção de par de chaves do AWS][aws-keypair]

Por fim, selecione **Inicializar Instâncias**, depois **Visualizar Instâncias**.  Você tem a base para o cluster do Service Fabric criado, agora você precisa adicionar algumas configurações finais nas próprias instâncias para prepará-las para a configuração do Service Fabric.

## <a name="modify-the-security-group"></a>Modificar o grupo de segurança

O Service Fabric requer uma quantidade de portas abertas entre os hosts no cluster. Para abrir essas portas na infraestrutura do AWS, selecione uma das instâncias que você criou. Em seguida, selecione o nome do grupo de segurança, por exemplo, **launch-wizard-1**. Agora, selecione a guia **Entrada**.

Para evitar a abertura dessas portas para o mundo, em vez disso, abra-as apenas para hosts no mesmo grupo de segurança. Anote a ID do grupo de segurança, no exemplo, é **sg-c4fb1eba**.  Em seguida, selecione **Editar**.

Em seguida, adicione quatro regras ao grupo de segurança para dependências de serviço e mais três para o Service Fabric em si. A primeira regra é permitir o tráfego ICMP para verificações de conectividade básica. As outras regras abrem as portas necessárias para habilitar o SMB e o registro remoto.

Para a primeira regra, selecione **Adicionar Regra**, depois, no menu suspenso, selecione **Todos ICMP – IPv4**. Selecione a caixa de entrada ao lado de personalizado e insira a ID do grupo de segurança acima.

Para as três últimas dependências, você precisa seguir um processo semelhante.  Selecione **Adicionar Regra**, na lista suspensa, selecione **Regra TCP Personalizada**, no intervalo de porta, insira `135`, `137-139` e `445` para cada regra. Por fim, na caixa de origem, insira a ID do grupo de segurança.

![Portas do grupo de segurança][aws-ec2securityports]

Agora que as portas para as dependências estão abertas, você precisa fazer o mesmo para as portas que o próprio Service Fabric usa para se comunicar. Selecione **Adicionar Regra**, na lista suspensa, selecione **Regra TCP Personalizada**, no intervalo de porta, insira `20001-20031` e insira o grupo de segurança na caixa de pesquisa.

Em seguida, adicione uma regra para o intervalo de portas efêmeras.  Selecione **Adicionar Regra**, na lista suspensa, selecione **Regra TCP Personalizada**, no intervalo de porta, insira `20606-20861`. Por fim, na caixa de origem, insira a ID do grupo de segurança.

Para as últimas duas regras do Service Fabric, abra-o para o mundo para que você possa gerenciar o cluster do Service Fabric no seu PC. Selecione **Adicionar Regra**, na lista suspensa, selecione **Regra TCP Personalizada**, no intervalo de porta, insira `19000-19003` e `19080-19081`, depois altere a lista suspensa Origem para Qualquer lugar.

Por fim, precisamos apenas abrir a porta 8080 para que você possa ver o aplicativo quando ele for implantado. Selecione **Adicionar Regra**, na lista suspensa, selecione **Regra TCP Personalizada**, no intervalo de porta, insira `8080`, depois altere a lista suspensa Origem para Qualquer lugar.

Agora todas as regras foram inseridas. Selecione **Salvar**.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Conectar-se a uma instância e validar a conectividade

Na guia do grupo de segurança, selecione **Instâncias** no menu à esquerda.  Selecione cada uma das instâncias que você criou e anote seus endereços IP privados; para os exemplos a seguir, serão usados `172.31.21.141` e `172.31.20.163`.

Assim que tiver todos os endereços IP, selecione uma das instâncias à qual se conectar, clique com botão direito do mouse na instância e selecione **Conectar**.  A partir daqui, você pode baixar o arquivo da RDP para essa instância específica.  Selecione **Baixar arquivo da área de trabalho remota**, depois abra o arquivo que é baixado para estabelecer sua conexão de área de trabalho remota (RDP) a essa instância.  Quando solicitado, insira sua senha `serv1ceF@bricP@ssword`.

![Baixar o arquivo de área de trabalho remoto][aws-rdp]

Depois que você se conectar à sua instância com sucesso, valide que você pode se conectar entre elas e também compartilhar arquivos.  Você reuniu os endereços IP de todas as instâncias, selecione um ao qual você não esteja conectado atualmente. Vá para **Iniciar**, digite `cmd` e selecione **Prompt de comando**.

Nesses exemplos, a conexão da RDP foi estabelecida com o seguinte endereço IP: 172.31.21.141. Assim, todo o teste de conectividade ocorre no outro endereço IP: 172.31.20.163.

Para validar se a conectividade básica funciona, use o comando ping.

```
ping 172.31.20.163
```

Se a saída se parecer com `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetido quatro vezes, isso significa que sua conexão entre as instâncias está funcionando.  Agora valide se seu compartilhamento SMB funciona com o seguinte comando:

```
net use * \\172.31.20.163\c$
```

Ele deve retornar `Drive Z: is now connected to \\172.31.20.163\c$.` como a saída.

## <a name="prep-instances-for-service-fabric"></a>Preparar instâncias do Service Fabric

Se você a estava criando do zero, precisa realizar algumas etapas adicionais.  Ou seja, você precisa validar que o registro remoto estava em execução, habilitar o SMB e abrir as portas necessárias para o SMB e o registro remoto.

Para facilitar, você inseriu todo esse trabalho quando inicializou as instâncias com o script de dados do usuário.

Para habilitar o SMB, o comando do PowerShell usado foi:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Este é o comando do PowerShell para abrir as portas no firewall:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu como inicializar três instâncias de EC2 e como configurá-las para a instalação do Service Fabric:

> [!div class="checklist"]
> * Criar um conjunto de instâncias de EC2
> * Modificar o grupo de segurança
> * Entrar em uma das instâncias
> * Preparar a instância do Service Fabric

Avance até segunda parte da série para configurar o Service Fabric no cluster.

> [!div class="nextstepaction"]
> [Instalar o Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png