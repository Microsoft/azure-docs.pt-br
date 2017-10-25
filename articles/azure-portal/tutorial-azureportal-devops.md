---
title: 'Tutorial: DevOps com o Portal do Azure | Microsoft Docs'
description: "Aprenda os vários fluxos de trabalho de DevOps no Portal do Azure."
services: azure-portal
documentationcenter: 
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2016
ms.author: mlearned
ms.openlocfilehash: b590fb06a3dba8aec66a380217269e1ca39bb5e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-devops-with-the-azure-portal"></a>Tutorial: DevOps com o Portal do Azure
A plataforma do Azure está repleta de fluxos de trabalho flexíveis do DevOps. Neste tutorial, você aprenderá a aproveitar os recursos do Portal do Azure para desenvolver, testar, implantar, solucionar problemas, monitorar e gerenciar aplicativos em execução. Este tutorial se concentra no seguinte:

1. Criar um aplicativo Web e habilitar a implantação contínua
2. Desenvolver e testar aplicativos
3. Monitorar e solucionar problemas de um aplicativo
4. Tarefas de gerenciamento de aplicativo móvel

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Criar um aplicativo Web e habilitar a implantação contínua
Crie um aplicativo Web com o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), que você usará no restante deste tutorial. Inicialmente, você habilitará a implantação contínua de seu repositório de código fonte em nosso ambiente do Azure em execução.

1. Entrar no Portal do Azure
2. Escolha **Serviços de Aplicativos** &gt; **ícone Adicionar** e insira um nome, escolha sua assinatura e crie um novo grupo de recursos para servir como contêiner para o serviço.
   
   Os grupos de recurso permitem que você gerencie vários aspectos da solução, por exemplo, cobrança, implantações e monitoramento, tudo como um único grupo por meio do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   
   ![image1][image1]
3. Após alguns instantes, o serviço de aplicativo será criado. Reserve alguns minutos para explorar as várias opções de menu do serviço no portal.
   
   ![image2][image2]    
4. Clique na URL. Observe as diversas opções disponíveis para ferramentas e repositórios. Você também pode usar as linguagens e estruturas que preferir, incluindo .NET, Java e Ruby.
   
   ![image3][image3]    
5. O Portal do Azure torna a implantação contínua um processo simples que envolve apenas algumas etapas simples. No Portal do Azure, escolha as configurações do ícone para o serviço de aplicativo que você acabou de criar.
   
   ![image4][image4]
   
   Na folha aberta à direita, role até a seção de publicação.
   
   ![image5][image5]
6. Em seguida, defina algumas configurações para habilitar a implantação contínua para o aplicativo. Clique em Origem da Implantação e depois em Escolher Origem. Observe as diversas opções disponíveis para origens de repositório.
   
   ![image6][image6]
7. Para este exemplo, escolha GitHub. Opcionalmente, escolha o repositório que preferir e configure as credenciais de autorização.
   
   ![image7][image7]
8. Após a autorização de seu repositório, escolha um projeto e a ramificação que você deseja implantar. Confira vários exemplos fictícios abaixo.
   
   ![image8][image8]
9. Depois de escolher o projeto e a ramificação, clique em ok. Você começará a ver notificações de uma implantação.
   
   ![image9][image9]
10. Navegue de volta até o GitHub para ver o webhook criado para integrar o repositório de controle da fonte no Azure. O Portal do Azure permite a integração com o GitHub com apenas algumas etapas simples.
    
    ![image10][image10]
11. Para demonstrar a implantação contínua, adicione rapidamente algum conteúdo ao repositório. Para ter um exemplo simples, adicione um arquivo de texto de exemplo a um repositório GitHub. Fique à vontade para usar .NET, Ruby, Python ou algum outro tipo de aplicativo com o Serviço de Aplicativo. Adicionar também se quiser um arquivo de texto, ASP.NET MVC, Java ou aplicativo Ruby ao repositório de sua escolha.
    
    ![image11][image11]
12. Depois de confirmar as alterações em seu repositório, você verá uma nova implantação ser iniciada na área de notificações do portal. Clique em Sincronizar, se você não vir rapidamente as alterações após a confirmação de seu repositório.
    
    ![image12][image12]
13. Neste ponto, se você tentar carregar a página do serviço de aplicativo, poderá receber um erro 403. Neste exemplo, isso ocorre porque não há uma configuração típica de documento padrão para a página, por exemplo, um arquivo como index.htm ou default.html. Você pode corrigir isso rapidamente com as ferramentas do Portal do Azure.  No Portal do Azure, escolha Configurações &gt; Configurações do Aplicativo.
    
     ![image13][image13]
14. Uma folha será aberta para as configurações do aplicativo. Insira o nome da página "SamplePage.html" e clique em Salvar. Reserve alguns minutos para explorar as outras configurações.
    
    ![Imagem14][image14]
15. Como opção, atualize a URL do navegador para garantir que você veja as alterações esperadas. Nesse caso, há um texto simples preenchendo a página. Cada alteração adicional no repositório resultaria em uma nova implantação automática.
    
    ![Imagem15][image15]
    
    É fácil habilitar a implantação contínua com o Portal do Azure. Você também pode compilar pipelines de liberação mais complexos e usar muitas outras técnicas com os sistemas existentes de controle de origem e integração contínua para implantação no Azure, por exemplo, aproveitando os sistemas automatizados de gerenciamento de compilação e liberação.

## <a name="develop-and-test-an-app"></a>Desenvolver e testar aplicativos
Depois, faça algumas alterações no código base e implante rapidamente essas alterações. Você também configurará alguns testes de desempenho para o aplicativo Web.

1. No Portal do Azure, escolha Serviços de Aplicativos no painel de navegação e localize seu Serviço de Aplicativo.
   
   ![Imagem16][image16]
2. Clique em Ferramentas
   
   ![Imagem17][image17]
3. Observe a categoria de desenvolver em Ferramentas. Aqui, há várias ferramentas úteis que nos permitem trabalhar com aplicativos sem sair do Portal do Azure. Clique em Console.
   
   ![Imagem18][image18]
4. Na janela do console, você pode emitir comandos ao vivo para seu aplicativo. Digite o comando dir e pressione enter. Observe que os comandos que exigem privilégios elevados não funcionam.
   
   ![Imagem19][image19]
5. Volte para a categoria Desenvolver e escolha Visual Studio Online. Observação: o Visual Studio Online agora se chama Visual Studio Team Services.
   
   ![Imagem20][image20]
6. Ative/desative a experiência de edição no navegador para seu Aplicativo.
   
   ![Imagem21][image21]
7. Uma extensão da Web é instalada para seu aplicativo. As extensões adicionam funcionalidades de forma rápida e fácil aos aplicativos no Azure. Observe alguns dos outros tipos de extensão disponíveis na captura de tela abaixo.
   
   ![Imagem22][image22]
8. Após a instalação da extensão do Visual Studio Online, clique em Ir.
   
   ![Imagem23][image23]
9. Uma guia do navegador é aberta e nela você pode ver um IDE de desenvolvimento diretamente no navegador. Observe que a experiência abaixo ocorre no Chrome.
   
   ![Imagem24][image24]
10. Você pode executar várias atividades, por exemplo, editar arquivos, adicionar arquivos e pastas e baixar o conteúdo do site ativo. Faça uma edição rápida no arquivo SamplePage.html.
    
    ![Imagem25][image25]
11. Em alguns instantes, as alterações serão salvas automaticamente. Se você navegar de volta para a página, poderá ver as alterações. Lembre-se de que edições ao vivo como essas provavelmente não são adequadas para ambientes de produção. No entanto, as ferramentas facilitam muito a realização de alterações rápidas para ambientes de desenvolvimento e de teste.
    
    ![Imagem26][image26]
    
    ![Imagem27][image27]
12. Volte para a folha de ferramentas e, na categoria Desenvolver, clique em Teste de Desempenho.
    
    ![Imagem28][image28]
13. Você precisa definir uma conta no Team Services. Confira aqui mais detalhes: [Criar uma conta do Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
14. Clique em Novo para criar um teste de desempenho.
    
    ![Imagem29][image29]
    
    Configure os diversos valores e clique em Executar Teste na parte inferior da caixa de diálogo para iniciar um teste de desempenho.
    
    ![Imagem30][image30]
    
    ![Imagem31][image31]
15. Assim que o teste começar, você poderá monitorar o estado.
    
    ![Imagem32][image32]
    
    Após a conclusão do teste, clique no resultado para mostrar mais detalhes.
    
    ![Imagem33][image33]
16. Neste exemplo, você criou uma pequena execução de teste, então há uma quantidade limitada para análise, mas é possível ver diversas métricas e executar novamente o teste a partir dessa perspectiva. O Portal do Azure facilita a criação, execução e análise de testes de desempenho da Web. As capturas de tela a seguir exibem os dados de desempenho.
    
    ![Imagem34][image34]
    
    ![Imagem35][image35]
    
    ![Imagem36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>Monitorar e solucionar problemas de um aplicativo
O Azure oferece muitos recursos de monitoramento e solução de problemas de aplicativos em execução.

1. No Portal do Azure de nosso aplicativo Web, escolha Ferramentas.
   
   ![Imagem37][image37]
2. Na categoria Solução de Problemas, observe as diversas opções de uso das ferramentas para solucionar possíveis problemas com um aplicativo em execução. Você pode fazer coisas como monitorar o tráfego Live HTTP, habilitar a autorrecuperação, exibir view e muito mais.
   
   ![Imagem38][image38]
3. Escolha as Métricas do Site para ver rapidamente alguns códigos HTTP.
   
   ![Imagem39][image39]
4. Escolha Diagnóstico como Serviço. Escolha o tipo de aplicativo e depois Executar.
   
   ![Imagem40][image40]
   
   Começa a coleta.
   
   ![Imagem41][image41]
5. Você pode escolher o log adequado para diagnosticar possíveis problemas. Você precisa habilitar o registro em log para ver todas as opções de dados disponíveis, como Logs de HTTP.
   
   ![Imagem42][image42]
   
   Ao clicar no arquivo de Despejo de Memória, você pode baixar e analisar um relatório de análise de DebugDiag para ajudar a localizar possíveis problemas.
   
   ![Imagem43][image43]
6. Para exibir mais dados, você precisa habilitar mais registro em log. No Portal do Azure, navegue até o aplicativo Web e escolha Configurações.
   
   ![Imagem44][image44]
7. Role a tela para baixo até a categoria de recursos e escolha Logs de diagnóstico.
   
      ![Imagem45][image45]
8. Observe as várias opções para registro em log. Ative o registro em log do servidor Web e clique em Salvar.
   
   ![Imagem46][image46]
9. Volte para a área de ferramentas do aplicativo e escolha Diagnóstico como serviço e clique em Executar, para executar novamente a coleta de dados.
   
   ![Imagem47][image47]
10. Com a configuração de registro em log HTTP habilitada, você verá os dados coletados para Logs de HTTP.
    
    ![Imagem48][image48]
11. Clicando no log de arquivo HTML, você produz um relatório detalhado baseado no navegador, a fim de obter mais informações.
    
    ![Imagem49][image49]
12. Volta para a seção de ferramentas no Portal do Azure do aplicativo. Role até a seção Ferramentas e escolha o Gerenciador de Processos.
    
    ![Imagem50][image50]
13. Ao escolher Gerenciador de Processos, é possível exibir detalhes sobre os processos em execução. Perceba abaixo que você pode detalhar, e até mesmo interromper, os processos, tudo do Portal do Azure.
    
    ![Imagem51][image51]
    
    ![Imagem52][image52]
14. Volte para a folha Configurações à esquerda. Clique em Nova solicitação de suporte.
    
    ![Imagem53][image53]
15. Na folha à direita, preencha os detalhes sobre os problemas, insira as informações de contato e carregue até mesmo dados de diagnóstico. O Portal do Azure permite uma experiência tranquila de trabalho com o suporte da Microsoft.
    
    ![Imagem54][image54]
    
    ![Imagem55][image55]
    
    O Portal do Azure ajuda a fornecer experiências com as ferramentas conhecidas e avançadas, para ajudar a monitorar e a solucionar problemas com os aplicativos em execução. Você também é capaz de agir rapidamente executando tarefas como processos de reciclagem, habilitação e desabilitação de diversas coletas de dados e até mesmo a integração com o suporte profissional da Microsoft.

## <a name="general-application-management"></a>Gerenciamento geral de aplicativos
Frequentemente, ao gerenciar aplicativos, você precisa executar várias atividades, como configuração de estratégias de backup, implementação e gerenciamento de provedores de identidade e configuração de controle de acesso baseado em função. Assim como ocorre com as outras experiências de DevOps, a plataforma do Azure integra essas tarefas diretamente no portal.

1. Para garantir a proteção do aplicativo Web contra perda de dados, você precisa configurar os backups. Navegue até a área Configurações de seu aplicativo Web.
   
   ![Imagem56][image56]
2. Na folha à direita, role para baixo até a categoria de Recursos.
   
    ![Imagem57][image57]
3. Escolha Backups; uma folha será aberta no lado direito.
   
   ![Imagem58][image58]
4. Clique em Configurar, escolha uma conta de armazenamento na folha à direita.
   
   ![Imagem59][image59]
5. Agora, crie e escolha um contêiner de armazenamento para guardar os backups. Clique em criar na parte inferior da folha. Em seguida, selecione o contêiner.
   
   ![Imagem60][image60]
6. Depois de escolher o contêiner, você pode configurar agendas, bem como configurar backups para seus bancos de dados. Para esse cenário, clique no ícone Salvar.
   
    ![Imagem61][image61]
7. Depois de salvar, role de volta para a folha à esquerda até Backups. Clique em Fazer Backup Agora para criar o backup do aplicativo.
   
    ![Imagem62][image62]
8. Em poucos instantes, verá um backup criado. Observe a opção Restaurar Agora na captura de tela abaixo.
   
    ![Imagem63][image63]
9. Clique em Restaurar Agora e examine as opções na folha à direita. Você pode escolher um backup apropriado e restaurar facilmente para um estado anterior, conforme o necessário. O Portal do Azure nos ajudou a habilitar de forma simples uma estratégia de recuperação de desastres simples para o aplicativo.
   
    ![Imagem64][image64]
10. Volte para a folha Configurações à esquerda e em Recursos, escolha Autenticação/Autorização.
    
     ![Imagem65][image65]
11. Na folha à direita, escolha Autenticação do Serviço de Aplicativo. Observe as várias opções que podem ser configuradas com provedores populares.
    
     ![Imagem66][image66]
12. Escolha o provedor de sua escolha e observe as opções para o escopo. Você pode fornecer uma ID do Aplicativo e o Segredo do Aplicativo e habilitar rapidamente a autenticação do Facebook para o aplicativo. O Portal do Azure permite a autenticação como uma solução completa para aplicativos.
    
     ![Imagem67][image67]
13. Volte para a folha Configurações e escolha Usuários na categoria Gerenciamento de Recursos.
    
     ![Imagem68][image68]
14. Na folha à direita, examine as várias opções para adição de usuários e funções. O Portal do Azure permite que você controle facilmente o RBAC (controle de acesso baseado em função) para o aplicativo.
    
     ![Imagem69][image69]

## <a name="summary"></a>Resumo
Este tutorial demonstrou um pouco das possibilidades com a plataforma do Azure por meio da habilitação da implantação contínua de um aplicativo Web, execução de várias atividades de desenvolvimento e de teste, monitoramento e solução de problemas de um aplicativo ativo e, por fim, gerenciamento de estratégias fundamentais, como recuperação de desastres, identidade e controle de acesso baseado em função. A plataforma do Azure permite uma experiência integrada para esses fluxos de trabalho DevOps, e você pode trabalhar com eficiência, conhecendo o contexto da tarefa em questão.

## <a name="next-steps"></a>Próximas etapas
* O Azure Resource Manager é importante para permitir DevOps na plataforma do Azure.  Para saber mais, visite [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para saber mais sobre implantação do Serviço de Aplicativo do Azure, visite [implantar seu aplicativo no Serviço de Aplicativo do Azure](../app-service/app-service-deploy-local-git.md)

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png
