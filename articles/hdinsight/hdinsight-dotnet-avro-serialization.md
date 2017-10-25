---
title: "Serializar dados no Hadoop – Biblioteca do Microsoft Avro – Azure | Microsoft Docs"
description: "Saiba como serializar e desserializar dados no Hadoop no HDInsight usando a Biblioteca do Microsoft Avro para manter a memória, um banco de dados ou um arquivo."
keywords: avro, hadoop avro
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: jgao
ms.custom: hdiseo17may2017
ms.openlocfilehash: d06bf8ff4a21e4f4b29593bac32bfa2b32601fc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializar dados no Hadoop com a Biblioteca do Microsoft Avro

>[!NOTE]
>A Microsoft não dá mais suporte ao SDK do Avro. A biblioteca tem suporte pela comunidade de software livre. As fontes para a biblioteca estão localizadas no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Este tópico mostra como usar a [Biblioteca da Microsoft Avro](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) para serializar objetos e outras estruturas de dados em fluxos para mantê-los na memória, em um banco de dados ou em um arquivo. Ele também mostra como desserializá-los para recuperar os objetos originais.

[!INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
A <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca do Microsoft Avro</a> implementa o sistema de serialização de dados Apache Avro para o ambiente Microsoft.NET. O Apache Avro fornece um formato de intercâmbio de dados binários compactos para serialização. Ele usa o <a href="http://www.json.org" target="_blank">JSON</a> para definir um esquema com neutralidade de linguagem que subscreve a interoperabilidade da linguagem. Os dados serializados em uma linguagem podem ser lidos em outra diferente. Atualmente, há suporte para C, C++, C#, Java, PHP, Python e Ruby. Você encontra informações detalhadas sobre o formato na <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Especificação do Apache Avro</a>. 

>[!NOTE]
>A Biblioteca do Microsoft Avro não dá suporte à parte sobre RPCs (chamadas de procedimento remoto) dessa especificação.
>

A representação serializada de um objeto no sistema Avro tem duas partes: o esquema e o valor real. O esquema do Avro descreve o modelo de dados independente de linguagem dos dados serializados com JSON. Ela é apresentada lado a lado, com uma representação binária dos dados. Colocar o esquema separado da representação binária permite que cada objeto seja programado sem sobrecargas por valor, tornando a serialização rápida e a representação pequena.

## <a name="the-hadoop-scenario"></a>O cenário do Hadoop
O formato de serialização do Apache Avro é amplamente utilizado no Azure HDInsight e em outros ambientes Apache Hadoop. O Avro fornece um meio conveniente de representar estruturas de dados complexas em um trabalho MapReduce do Hadoop. O formato dos arquivos do Avro (arquivos contêiner de objetos do Avro) foi projetado para dar suporte ao modelo de programação distribuído do MapReduce. O recurso principal que habilita a distribuição é o fato de os arquivos serem divisíveis, no sentido de que uma pessoa pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco em específico.

## <a name="serialization-in-avro-library"></a>Serialização na biblioteca de Avro
A Biblioteca do Microsoft .NET para Avro suporta dois modos de serialização de objetos:

* **reflexão** : o esquema JSON dos tipos é criado automaticamente com base nos atributos do contrato de dados dos tipos .NET a serem serializados.
* **registro genérico** - um esquema JSON é especificado explicitamente em um registro representado pela classe [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) quando nenhum tipo .NET está presente para descrever o esquema dos dados a serem serializados.

Quando o esquema de dados é conhecido tanto pelo gravador quanto pelo leitor do fluxo, os dados podem ser enviados sem o seu esquema. Caso um arquivo contêiner de objetos do Avro seja usado, o esquema será armazenado no arquivo. Você pode especificar outros parâmetros, como o codec usado para a compactação dos dados. Esses cenários são descritos com mais detalhes e ilustrados nos exemplos de código a seguir:

## <a name="install-avro-library"></a>Instalar a Biblioteca do Avro
Os itens a seguir são necessários antes de instalar a biblioteca:

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 ou posterior)

Observe que a dependência Newtonsoft.Json.dll também é baixada automaticamente com a instalação da Biblioteca do Microsoft Avro. O procedimento é fornecido na seção a seguir:

A Biblioteca do Microsoft Avro é distribuída como um pacote NuGet, que pode ser instalado pelo Visual Studio usando este procedimento:

1. Selecione a guia **Projeto** -> **Gerenciar Pacotes NuGet...**
2. Pesquise "Microsoft.Hadoop.Avro" na caixa **Pesquisar Online** .
3. Clique no botão **Instalar** próximo à **Biblioteca Avro do Microsoft Azure HDInsight**.

Observe que a dependência Newtonsoft.Json.dll (>=6.0.4) também é baixada automaticamente com a Biblioteca do Microsoft Avro.

O código-fonte da Biblioteca do Microsoft Avro está disponível no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Compilar esquemas usando a Biblioteca do Avro
A Biblioteca do Microsoft Avro contém um utilitário de geração de código que permite criar tipos C# de maneira automática, com base no esquema JSON definido anteriormente. O utilitário de geração de código não é distribuído como um executável binário, mas pode ser facilmente compilado com este procedimento:

1. Baixe o arquivo .zip com a versão mais recente do código-fonte do SDK do HDInsight em <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">SDK do Microsoft .NET para Hadoop</a>. (Clique no ícone **Baixar**, não na guia **Downloads**.)
2. Extraia o SDK do HDInsight para um diretório do computador com o .NET Framework 4 instalado e conectado à Internet para baixar os pacotes NuGet de dependência necessários. No exemplo a seguir, vamos supor que o código-fonte seja extraído para C:\SDK.
3. Vá para a pasta C:\SDK\src\Microsoft.Hadoop.Avro.Tools e execute build.bat. (O arquivo chama o MSBuild da distribuição de 32 bits do .NET Framework. Se quiser usar a versão de 64 bits, edite o build.bat seguindo os comentários dentro do arquivo.) Confira se a compilação foi bem-sucedida. (Em alguns sistemas, o MSBuild pode gerar avisos. Esses avisos não afetam o utilitário, desde que não haja erros de compilação.)
4. O utilitário compilado está localizado em C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Para se familiarizar com o a sintaxe de linha de comando, execute este comando na pasta em que se encontra o utilitário de geração de código:`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Para testar o utilitário, você pode gerar classes C# por meio do arquivo de esquema JSON de exemplo fornecido com o código-fonte. Execute o seguinte comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Isso deve produzir dois arquivos C# no diretório atual: SensorData.cs e Location.cs.

Para entender a lógica usada pelo o utilitário de geração de código ao converter o esquema JSON em tipos C#, consulte o arquivo GenerationVerification.feature localizado em C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Os namespaces são extraídos do esquema JSON usando a lógica descrita no arquivo mencionado no parágrafo anterior. Os namespaces extraídos do esquema têm prioridade sobre qualquer coisa fornecida com o parâmetro /n na linha de comando do utilitário. Se quiser substituir os namespaces que estão no esquema, use o parâmetro /nf. Por exemplo, para alterar todos os namespaces de SampleJSONSchema.avsc para my.own.nspace, execute este comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>Sobre as amostras
Cada um dos seis exemplos fornecidos neste tópico ilustra um cenário diferente com suporte pela Biblioteca do Microsoft Avro. A Biblioteca do Microsoft Avro foi projetada para funcionar com qualquer fluxo. Nestes exemplos, os dados são manipulados por fluxos de memória em vez de fluxos de arquivo ou bancos de dados, por questão de simplicidade e consistência. A abordagem usada em um ambiente de produção depende dos requisitos exatos do cenário, tais como fonte de dados e volume, restrições de desempenho e outros fatores.

Os primeiros dois exemplos mostram como serializar e desserializar os dados nos buffers de fluxo de memória usando reflexão e registros genéricos. Presume-se que o esquema nesses dois casos é compartilhado entre os leitores e gravadores fora de banda.

O terceiro e quarto exemplos mostram como serializar e desserializar os dados usando os arquivos contêiner de objetos do Avro. Quando os dados são armazenados em um arquivo contêiner do Avro, o esquema é sempre armazenado junto com o arquivo, uma vez que o esquema precisa ser compartilhado para a desserialização.

O exemplo que contém os primeiros quatro exemplos pode ser baixado do site <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">Exemplos de código do Azure</a> .

O quinto exemplo mostra como usar um codec de compactação personalizado para arquivos contêiner de objetos do Avro. Uma amostra contendo o código para esse exemplo pode ser baixada no site <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">Exemplos de código do Azure</a> .

O sexto exemplo mostra como usar a serialização do Avro para carregar dados no armazenamento de Blob do Azure e analisá-los usando o Hive com um cluster HDInsight (Hadoop). Ele pode ser baixado do site <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Exemplos de código do Azure</a> .

Aqui estão os links aos seis exemplos abordados neste tópico:

* <a href="#Scenario1">**Serialização com reflexão**</a> - o esquema JSON dos tipos a serem serializados é criado automaticamente a partir dos atributos do contrato dos dados.
* <a href="#Scenario2">**Serialização com registro genérico**</a> - o esquema JSON é especificado explicitamente em um registro quando nenhum tipo .NET está disponível para a reflexão.
* <a href="#Scenario3">**Serialização usando arquivos do contêiner de objetos com reflexão**</a> - o esquema JSON é automaticamente criado e compartilhado junto com os dados serializados por meio de um arquivo do contêiner de objetos do Avro.
* <a href="#Scenario4">**Serialização usando arquivos do contêiner de objetos com registro genérico**</a> - o esquema JSON é explicitamente especificado antes da serialização e compartilhado junto com os dados por meio de um arquivo do contêiner de objetos do Avro.
* <a href="#Scenario5">**Serialização usando arquivos do contêiner de objetos com um codec de compactação personalizado**</a> - o exemplo mostra como criar um arquivo do contêiner de objetos do Avro com uma implementação .NET personalizada do codec de compactação de dados Deflate.
* <a href="#Scenario6">**Usando o Avro para carregar dados para o serviço Microsoft Azure HDInsight**</a> - o exemplo ilustra como a serialização do Avro interage com o serviço HDInsight. Você precisa de uma assinatura ativa do Azure e acesso a um cluster Azure HDInsight para executar este exemplo.

## <a name="Scenario1"></a>Exemplo 1: Serialização com reflexão
O esquema JSON dos tipos pode ser compilado automaticamente pela Biblioteca do Microsoft Avro por reflexão, com base nos atributos do contrato de dados dos objetos C# a serem serializados. A Biblioteca do Microsoft Avro cria um [**IAvroSeralizer<T>**](http://msdn.microsoft.com/library/dn627341.aspx) para identificar os campos a serem serializados.

Neste exemplo, os objetos (uma classe **SensorData** com um struct membro **Local**) são serializados para um fluxo de memória, que por sua vez é desserializado. O resultado é, então, comparado à instância inicial para confirmar que o objeto **SensorData** recuperado é idêntico ao original.

Presume-se que o esquema neste exemplo é compartilhado entre os leitores e gravadores, portanto, o formato do contêiner de objetos do Avro não é exigido. Para ver um exemplo de como serializar e desserializar dados em buffers de memória usando reflexão com o formato de contêiner de objetos quando o esquema precisa ser serializado com os dados, consulte <a href="#Scenario3">Serialização usando arquivos contêiner de objetos com reflexão.</a>

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-2-serialization-with-a-generic-record"></a>Exemplo 2: serialização com um registro genérico
Um esquema JSON pode ser especificado explicitamente em um registro genérico quando não é possível usar reflexão porque os dados não podem ser representados por classes .NET com um contrato de dados. Este método é mais lento do que usar reflexão. Nesses casos, o esquema dos dados também pode ser dinâmico, ou seja, pode não ser conhecido no tempo de compilação. Um exemplo desse tipo de cenário dinâmico são dados representados como arquivos CSV (valores separados por vírgulas) com esquema desconhecido até serem transformados no formato Avro em tempo de execução.

Este exemplo mostra como criar e usar um [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) para especificar explicitamente um esquema JSON e como preenchê-lo com dados para, depois, serializá-lo e desserializá-lo. O resultado é, então, comparado à instância inicial para confirmar que o registro recuperado é idêntico ao original.

Presume-se que o esquema neste exemplo é compartilhado entre os leitores e gravadores, portanto, o formato do contêiner de objetos do Avro não é exigido. Para ver um exemplo de como serializar e desserializar dados em buffers de memória usando um registro genérico com o formato de contêiner de objetos quando o esquema deve ser incluído com os dados serializados, consulte o exemplo <a href="#Scenario4">Serialização usando arquivos contêiner de objetos com registro genérico</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exemplo 3: serialização usando arquivos do contêiner de objetos e serialização com reflexão
Este exemplo é semelhante ao cenário do <a href="#Scenario1"> primeiro exemplo</a>, cujo esquema é especificado implicitamente com reflexão. A diferença é que, aqui, não presumimos que o esquema seja conhecido do leitor que o desserializa. Os objetos **SensorData** a serem serializados e seu esquema especificado implicitamente são armazenados em um arquivo do contêiner de objetos Avro representado pela classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx).

Os dados são serializados neste exemplo com [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx) e desserializados com [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). O resultado é, então, comparado com as instâncias iniciais para garantir a identidade.

Os dados no arquivo contêiner de objetos são compactados pelo codec de compactação padrão [**Deflate**][deflate-100] do .NET Framework 4. Consulte o <a href="#Scenario5"> quinto exemplo</a> neste tópico para aprender a usar uma versão mais recente e superior do codec de compactação [**Deflate**][deflate-110], disponível no .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exemplo 4: serialização usando arquivos do contêiner de objetos e serialização com registro genérico
Este exemplo é semelhante ao cenário do <a href="#Scenario2"> segundo exemplo</a>, cujo esquema é especificado explicitamente com JSON. A diferença é que, aqui, não presumimos que o esquema seja conhecido do leitor que o desserializa.

O conjunto de dados de teste é coletado em uma lista de objetos [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) por meio de um esquema JSON definido explicitamente e armazenado em um arquivo do contêiner de objetos representado pela classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx). Esse arquivo contêiner cria um gravador usado para serializar os dados descompactados em um fluxo de memória que é salvo em um arquivo. O parâmetro [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) usado para criar o leitor especifica que esses dados não são compactados.

Em seguida, os dados são lidos a partir do arquivo e desserializados em uma coleção de objetos. Essa coleção é comparada à lista inicial de registros do Avro para confirmar que são idênticos.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exemplo 5: serialização usando arquivos do contêiner de objetos com um codec de compactação personalizado
O quinto exemplo mostra como usar um codec de compactação personalizado para arquivos contêiner de objetos do Avro. Uma amostra contendo o código para esse exemplo pode ser baixada no site [Exemplos de código do Azure](http://code.msdn.microsoft.com/Serialize-data-with-the-67159111) .

A [Especificação Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permite o uso de um codec de compactação opcional (além dos padrões **Null** e **Deflate**). Este exemplo não implementa um codec novo como o Snappy (mencionado como codec opcional compatível na [Especificação do Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Ele mostra como usar a implementação do .NET Framework 4.5 do codec [**Deflate**][deflate-110], que oferece um algoritmo de compactação baseado na biblioteca de compactação [zlib](http://zlib.net/) melhor do que a versão padrão do .NET Framework 4.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exemplo 6: usando o Avro para carregar dados para o serviço do Microsoft Azure HDInsight
O sexto exemplo ilustra algumas técnicas de programação relacionadas à interação com o serviço do Microsoft Azure HDInsight. Uma amostra contendo o código para esse exemplo pode ser baixada no site [Exemplos de código do Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) .

O exemplo a seguir realiza as seguintes tarefas:

* Conecta-se a um cluster de serviço do HDInsight existente.
* Serializa diversos arquivos CSV e carrega o resultado na Armazenamento de Blob do Azure. (Os arquivos CSV são distribuídos junto com o exemplo e representam amostras de dados históricos da Bolsa de Valores AMEX distribuídos pela [Infochimps](http://www.infochimps.com/) para o período de 1970-2010. O exemplo lê os dados dos arquivos CSV, converte os registros em instâncias da classe **Stock** (Ações) e os serializa usando reflexão. A definição do tipo de ação é criada com base no esquema JSON pelo utilitário de geração de código da Biblioteca do Microsoft Avro.
* Cria uma nova tabela externa chamada **Stocks** (Ações) no Hive e a vincula aos dados carregados na etapa anterior.
* Executa uma consulta usando o Hive na tabela **Stocks** .

Além disso, o exemplo realiza um procedimento de limpeza antes e depois da realização de operações mais importantes. Durante a limpeza, todos os dados e pastas de Blob do Azure relacionados são removidos e a tabela do Hive é removida. Você também pode invocar um procedimento de limpeza na linha de comando do exemplo.

O exemplo tem os seguintes pré-requisitos:

* Uma assinatura do Microsoft Azure ativa e a ID de assinatura correspondente.
* Um certificado de gerenciamento da assinatura com a chave privada correspondente. O Certificado deve ser instalado no armazenamento privado do usuário atual no computador usado para executar o exemplo.
* Um cluster HDInsight ativo.
* Uma conta de Armazenamento do Azure vinculada ao cluster HDInsight do pré-requisito anterior, juntamente com a chave de acesso primária ou secundária correspondente.

Todas as informações dos pré-requisitos devem ser inseridas no arquivo de configuração do exemplo antes deste ser executado. Há duas maneiras possíveis de fazer isso:

* Editar o arquivo app.config no diretório raiz do exemplo e compilar o exemplo
* Primeiro compilar o exemplo e, depois, editar o AvroHDISample.exe.config no diretório de compilação

Em ambos os casos, todas as edições devem ser feitas na seção de configurações **<appSettings>**. Siga os comentários no arquivo.
O exemplo é executado na linha de comando pela execução do seguinte comando (presume-se que o arquivo .zip com o exemplo tenha sido extraído para C:\AvroHDISample; caso contrário, use o caminho de arquivo relevante):

    AvroHDISample run C:\AvroHDISample\Data

Para limpar o cluster, execute este comando:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
