---
title: Aplicativo Java que requer muitos recursos computacionais em uma VM | Microsoft Docs
description: "Saiba como criar uma máquina virtual do Azure que execute aplicativos Java que requerem muitos recursos computacionais e podem ser monitorados por outro aplicativo Java."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 8c51c0bb37e25ad61fe58a85dd641dabe0a1958c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Como executar uma tarefa com uso intenso de computação no Java em uma máquina virtual
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Com o Azure, você pode usar uma máquina virtual para lidar com tarefas de computação intensiva. Por exemplo, uma máquina virtual pode lidar com tarefas e fornecer resultados às máquinas dos clientes ou aos aplicativos móveis. Depois de ler este artigo, você terá um entendimento de como criar uma máquina virtual que executa um aplicativo Java de computação intensiva que pode ser monitorado por outro aplicativo Java.

Este tutorial supõe que você sabe como criar aplicativos de console Java, pode importar bibliotecas para o seu aplicativo Java e pode gerar um arquivo Java (JAR). Nenhum conhecimento do Microsoft Azure é assumido.

Você aprenderá:

* Como criar uma máquina virtual que já tenha um JDK (Java Development Kit) instalado.
* Fazer logon remotamente na máquina virtual.
* Como criar um espaço de nomes de barramento de serviço.
* Como criar um aplicativo Java que executa uma tarefa de computação intensiva.
* Como criar um aplicativo Java que monitora o andamento da tarefa de computação intensiva.
* Como executar os aplicativos Java.
* Como parar os aplicativos Java.

Este tutorial usará o problema do Caixeiro Viajante para a tarefa de computação intensiva. Este é um exemplo do aplicativo Java que executa a tarefa de computação intensiva.

![Solucionador de problemas do Caixeiro Viajante][solver_output]

Este é um exemplo do aplicativo Java que monitora a tarefa de computação intensiva.

![Cliente de problemas do Caixeiro Viajante][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual
1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Nova**, clique em **Computação**, clique em **Máquina virtual** e, em seguida, clique em **Da Galeria**.
3. Na caixa de diálogo **Seleção de imagem da máquina virtual**, selecione **JDK 7 Windows Server 2012**.
   Observe que o **JDK 6 Windows Server 2012** está disponível caso você tenha aplicativos legados que ainda não estejam prontos para serem executados no JDK 7.
4. Clique em **Avançar**.
5. Na caixa de diálogo **Configuração da máquina virtual** :
   1. Especifique um nome para a máquina virtual.
   2. Especifique o tamanho a ser usado para a máquina virtual.
   3. Digite um nome para o administrador no campo **Nome do Usuário** . Lembre-se do nome e da senha que você digitará a seguir, você irá usá-los ao fazer logon remotamente na máquina virtual.
   4. Digite uma senha no campo **Nova senha** e insira-a novamente no campo **Confirmar**. Esta é a senha da conta do Administrador.
   5. Clique em **Avançar**.
6. Na próxima caixa de diálogo **Configuração da máquina virtual** :
   1. Para **Serviço de Nuvem**, use o padrão **Criar um novo serviço de nuvem**.
   2. O valor de **Nome DNS do Serviço de Nuvem** deve ser exclusivo no cloudapp.net. Se necessário, modifique esse valor para que o Azure indique que ele é exclusivo.
   3. Especifique uma região, um grupo de afinidade ou uma rede virtual. Para o objetivo deste tutorial, especifique uma região, como **Oeste dos Estados Unidos**.
   4. Para **Conta de Armazenamento**, selecione **Usar uma conta de armazenamento gerada automaticamente**.
   5. Para **Conjunto de Disponibilidade**, selecione **(Nenhum)**.
   6. Clique em **Avançar**.
7. Na caixa de diálogo **Configuração da máquina virtual** final:
   1. Aceite as entradas de ponto de extremidade padrão.
   2. Clique em **Concluído**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Para fazer logon remotamente na máquina virtual
1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Máquinas Virtuais**.
3. Clique no nome da máquina virtual na qual você deseja fazer logon.
4. Clique em **Conectar**.
5. Responda às solicitações conforme necessário para se conectar à máquina virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando criou a máquina virtual.

Observe que a funcionalidade do Service Bus do Azure requer que o certificado de Baltimore CyberTrust Root seja instalado como parte do armazenamento **cacerts** do JRE. Este certificado é automaticamente incluído no Java Runtime Environment (JRE) usado por este tutorial. Se você não tem este certificado no armazenamento **cacerts** do JRE, consulte [Adicionar um certificado no repositório de certificados de autoridade de certificação Java][add_ca_cert] para obter informações sobre como adicioná-lo (bem como informações sobre como exibir os certificados no seu armazenamento cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Como criar um namespace do barramento de serviço
Para começar a usar filas do Barramento de Serviço no Azure, primeiro crie um namespace de serviço. Um namespace de serviço fornece um contêiner de controle para endereçamento dos recursos do Barramento de Serviço em seu aplicativo.

Para criar um namespace de serviço:

1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel de navegação esquerdo inferior do portal clássico do Azure, clique em **Barramento de Serviço, Controle de Acesso e Caching**.
3. No painel superior esquerdo do portal clássico do Azure, clique no nó de **Barramento de Serviço** e, em seguida, clique no botão **Novo**.  
   ![Captura de tela do nó do Service Bus][svc_bus_node]
4. Na caixa de diálogo **Criar um novo namespace de serviço**, digite um **Namespace** e, em seguida, para verificar se ele é exclusivo, clique no botão **Verificar Disponibilidade**.  
   ![Criar uma captura de tela do novo Namespace][create_namespace]
5. Depois de verificar se o nome do namespace está disponível, escolha o país ou a região na qual o namespace deve estar hospedado e, em seguida, clique no botão **Criar Namespace** .  
   
   O namespace que você criou aparecerá no portal clássico do Azure e demorará algum tempo para ser ativado. Aguarde até que o status esteja **Ativo** para passar à próxima etapa.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Obter as Credenciais de gerenciamento padrão do namespace
A fim de executar operações de gerenciamento, como criar uma fila no novo namespace, você precisar obter as credenciais de gerenciamento para o namespace.

1. No painel de navegação esquerdo, clique no nó **Barramento de Serviço** para exibir a lista de namespaces disponíveis.
   ![Captura de tela de namespaces disponíveis][avail_namespaces]
2. Selecione o namespace que você acabou de criar na lista exibida.
   ![Captura de tela da lista de namespaces][namespace_list]
3. O painel direito **Propriedades** listará as propriedades do novo namespace.
   ![Captura de tela do painel Propriedades][properties_pane]
4. A **Chave padrão** está oculta. Clique no botão **Exibir** para exibir as credenciais de segurança.
   ![Captura de tela da chave padrão][default_key]
5. Anote o **Emissor Padrão** e a **Chave Padrão**, pois você usará essas informações abaixo para executar operações com o namespace.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Como criar um aplicativo Java que executa uma tarefa de computação intensiva
1. Na sua máquina de desenvolvimento (que não tem de ser a máquina virtual que você criou), faça o download do [Azure SDK para Java](https://azure.microsoft.com/develop/java/).
2. Crie um aplicativo de console Java usando o código de exemplo no final desta seção. Neste tutorial, usaremos **TSPSolver.java** como o nome do arquivo Java. Modifique os espaços reservados **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** e **your\_service\_bus\_key** para usar o **namespace** do barramento de serviço e os valores **Emissor Padrão** e **Chave Padrão**, respectivamente.
3. Depois de codificar, exporte o aplicativo para um arquivo executável Java (JAR) e empacote as bibliotecas necessárias para o JAR gerado. Neste tutorial, usaremos **TSPSolver.jar** como o nome do arquivo JAR gerado.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Como criar um aplicativo Java que monitora o andamento da tarefa de computação intensiva
1. Na sua máquina de desenvolvimento, crie um aplicativo de console Java usando o código de exemplo no final desta seção. Neste tutorial, usaremos **TSPClient.java** como o nome do arquivo Java. Conforme mostrado anteriormente, modifique os espaços reservados **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** e **your\_service\_bus\_key** para usar o **namespace** do barramento de serviço e os valores **Emissor Padrão** e **Chave Padrão**, respectivamente.
2. Exporte o aplicativo para um JAR executável e empacote as bibliotecas necessárias para o JAR gerado. Neste tutorial, usaremos **TSPClient.jar** como o nome do arquivo JAR gerado.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Como executar os aplicativos Java.
Execute o aplicativo que exija muita computação, primeiro para criar a fila, depois para solucionar o Problema do Caixeiro Viajante, que adicionará a melhor rota atual para a fila do barramento de serviço. Enquanto o aplicativo que exige muita computação está em execução (ou depois), execute o cliente para exibir os resultados da fila do barramento de serviço.

### <a name="to-run-the-compute-intensive-application"></a>Executar o aplicativo exige computação intensiva
1. Faça logon na máquina virtual.
2. Crie uma pasta onde você executará seu aplicativo. Por exemplo, **c:\TSP**.
3. Copie **TSPSolver.jar** para **c:\TSP**,
4. Crie um arquivo chamado **c:\TSP\cities.txt** com o conteúdo abaixo.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. Em um prompt de comando, altere os diretórios para c:\TSP.
6. Certifique-se de que a pasta da lixeira do JRE está na variável de ambiente PATH.
7. Você precisará criar a fila do barramento de serviço antes de executar as permutas de solver TSP. Execute o seguinte comando para criar a fila do barramento de serviço.
   
        java -jar TSPSolver.jar createqueue
8. Agora que a fila está criada, você pode executar as permutas de solver TSP. Por exemplo, execute o seguinte comando para executar o solver para 8 cidades.
   
        java -jar TSPSolver.jar 8
   
   Se você não especificar um número, ele será executado para 10 cidades. Como localiza rotas atuais mais curtas, o solver as adicionará à fila.

> [!NOTE]
> Quanto maior o número que você especificar, por mais tempo o solver será executado. Por exemplo, a execução de 14 cidades pode levar vários minutos, e a execução de 15 cidades pode levar várias horas. Aumentar para 16 ou mais cidades pode resultar em dias de tempo de execução (acabando em semanas, meses e anos). Isso ocorre porque o rápido aumento do número de permutas avaliadas pelo solver, como o número de cidades, aumenta.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Como executar o aplicativo cliente de monitoramento
1. Faça logon no computador onde você executará o aplicativo cliente. Ele não precisa estar na mesma máquina executando o aplicativo **TSPSolver** , embora possa ser.
2. Crie uma pasta onde você executará seu aplicativo. Por exemplo, **c:\TSP**.
3. Copie **TSPClient.jar** para **c:\TSP**,
4. Certifique-se de que a pasta da lixeira do JRE está na variável de ambiente PATH.
5. Em um prompt de comando, altere os diretórios para c:\TSP.
6. Execute o comando a seguir.
   
        java -jar TSPClient.jar
   
    Como opção, especifique o número de minutos de suspensão entre a verificação da fila passando um argumento de linha de comando. O período de suspensão padrão para a verificação da fila é de 3 minutos, que será usado se nenhum argumento de linha de comando for transmitido para **TSPClient**. Se você quiser usar um valor diferente para o intervalo de suspensão, como um minuto, por exemplo, execute o comando a seguir.
   
        java -jar TSPClient.jar 1
   
    O cliente será executado até ver a mensagem de uma fila "Concluído". Observe que, se executar várias ocorrências do solver sem executar o cliente, você precisará executar o cliente várias vezes para esvaziar completamente a fila. Também é possível excluir a fila e depois criá-la novamente. Para excluir a fila, execute o comando do **TSPSolver** (não **TSPClient**) a seguir.
   
        java -jar TSPSolver.jar deletequeue
   
    O solver será executado até terminar de examinar todas as rotas.

## <a name="how-to-stop-the-java-applications"></a>Como parar os aplicativos Java.
Para os aplicativos solver e de cliente, é possível pressionar **Ctrl+C** para sair se você quiser encerrar antes da conclusão normal.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
