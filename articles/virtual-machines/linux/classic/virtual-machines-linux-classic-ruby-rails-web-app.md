---
title: Hospedar um site Ruby on Rails em uma VM do Linux | Microsoft Docs
description: "Configurar e hospedar um site da Web baseado no Ruby on Rails no Azure usando uma máquina virtual do Linux."
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: 0518519da6c5e62a863a47d6743ab7b7c5923acf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Aplicativo Web Ruby on Rails Web em uma VM do Azure
Esse tutorial descreve como hospedar um site Ruby on Rails no Azure usando uma máquina virtual do Linux.  

Este tutorial foi validado usando o Ubuntu Server 14.04 LTS. Se você usar uma distribuição Linux diferente, talvez seja necessário modificar as etapas para instalar o Rails.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.
>
>

## <a name="create-an-azure-vm"></a>Criar uma VM do Azure
Comece criando uma VM do Azure com uma imagem do Linux.

Para criar a VM, você pode usar o Portal do Azure ou a CLI (interface de linha de comando) do Azure.

### <a name="azure-portal"></a>Portal do Azure
1. Entre no [Portal do Azure](https://portal.azure.com)
2. Clique em **Novo**, digite "Servidor do Ubuntu 14.04" na caixa de pesquisa. Clique na entrada retornada pela pesquisa. Para o modelo de implantação, selecione **Clássico** e, em seguida, clique em "Criar".
3. Na folha Noções básicas, forneça valores para os campos obrigatórios: Nome (para a VM), Nome de usuário, Tipo de autenticação e as credenciais correspondentes, Assinatura do Azure, Grupo de recursos e Local.

   ![Criar uma nova imagem Ubuntu](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Após o provisionamento da VM, clique no nome da VM e clique em **Pontos de extremidade** na categoria **Configurações**. Localize o ponto de extremidade do SSH, listado sob **Autônomo**.

   ![Ponto de extremidade padrão](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>CLI do Azure
Siga as etapas em [Como criar uma máquina virtual personalizada que executa o Linux][vm-instructions].

Depois que a VM for provisionada, você pode obter o ponto de extremidade do SSH executando o seguinte comando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Instalar o Ruby on Rails
1. Use SSH para conectar-se à VM.
2. Na sessão de SSH, use os seguintes comandos para instalar o Ruby na VM:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    A instalação pode levar alguns minutos. Quando for concluído, use o seguinte comando para verificar se o Ruby está instalado:

        ruby -v

3. Use o comando a seguir para instalar o Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    Use-sinalizadores --no-rdoc e --no-ri para instalar a documentação, o que é mais rápido.
    Provavelmente, este comando demorará muito para ser executado; portanto, ao adicionar -V serão exibidas informações sobre o progresso da instalação.

## <a name="create-and-run-an-app"></a>Criar e executar um aplicativo
Enquanto ainda estiver conectado via SSH, execute os seguintes comandos:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

O comando [new](http://guides.rubyonrails.org/command_line.html#rails-new) cria um novo aplicativo Rails. O comando [server](http://guides.rubyonrails.org/command_line.html#rails-server) inicia o servidor Web WEBrick que acompanha o Rails. (Para uso em produção, você provavelmente desejaria usar um servidor diferente, como Unicorn ou Passenger.)

Você deve ver saídas semelhantes às seguintes.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Adicionar um ponto de extremidade
1. Acesse o [Portal do Azure] [https://portal.azure.com] e selecione sua VM.

2. Selecione **PONTOS DE EXTREMIDADE** em **Configurações** na borda esquerda da página.

3. Clique em **ADICIONAR** na parte superior da página.

4. Na página de diálogo **Adicionar ponto de extremidade**, insira as seguintes informações:

   * **Nome**: HTTP
   * **Protocolo**: TCP
   * **Porta pública**: 80
   * **Porta privada**: 3000
   * **Endereço PI flutuante**: Desabilitado
   * **Lista de controle de acesso - Ordem**: 1001, ou outro valor que defina a prioridade desta regra de acesso.
   * **Lista de controle de acesso - Nome**: allowHTTP
   * **Lista de controle de acesso - Ação**: permitir
   * **Lista de controle de acesso - Sub-rede remota**: 1.0.0.0/16

     Esse ponto de extremidade tem uma porta pública de 80 que roteará o tráfego para a porta privada de 3000, na qual o servidor Rails está escutando. A regra da lista de controle de acesso permite o tráfego o público na porta 80.

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Clique em OK para salvar o ponto de extremidade.

6. Uma mensagem deverá aparecer indicando **Salvando o ponto de extremidade da máquina virtual**. Depois que a mensagem desaparecer, o ponto de extremidade estará ativo. Você já pode testar seu aplicativo navegando até o nome DNS de sua máquina virtual. O site deve aparecer ao seguinte:

    ![página padrão de rails][default-rails-cloud]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizou a maior parte das etapas manualmente. Em um ambiente de produção, você gravaria seu aplicativo em um computador de desenvolvimento e o implantaria em VM do Azure. Além disso, a maioria dos ambientes de produção hospeda o aplicativo Rails em conjunto com outro processo do servidor como Apache ou NginX, que trata o roteamento da solicitação para várias instâncias do aplicativo Rails e atende recursos estáticos. Para obter mais informações, consulte http://rubyonrails.org/deploy/.

Para saber mais sobre o Ruby on Rails, visite os [Guias do Ruby on Rails][rails-guides].

Para usar os serviços do Azure de seu aplicativo Ruby, consulte:

* [Armazenar dados desestruturados usando blobs][blobs]
* [Armazenar pares de chave/valor usando tabelas][tables]
* [Fornecer conteúdo de alta largura de banda com a Rede de Distribuição de Conteúdo][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
