<properties
	pageTitle="Persistência de saída de tarefas e trabalhos no Lote do Azure | Microsoft Azure"
	description="Saiba como usar o Armazenamento do Azure como um armazenamento durável para a saída de trabalhos e tarefas do Lote e habilitar a exibição dessa saída persistente no portal do Azure."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/06/2016"
	ms.author="marsma" />

# Persistir trabalho de Lote do Azure e saída de tarefa

As tarefas que você executa normalmente no Lote produzem saída que deve ser armazenada e recuperada posteriormente por outras tarefas no trabalho, no aplicativo cliente que executou o trabalho ou em ambos. Essa saída pode consistir em arquivos criados pelo processamento de dados de entrada ou arquivos de log associados à execução da tarefa. Este artigo apresenta uma biblioteca de classes .NET que usa uma técnica de convenções para persistir essa saída de tarefa para o armazenamento de Blobs do Azure, disponibilizando-a mesmo depois que você exclui os pools, trabalhos e nós de computação.

Usando a técnica deste artigo, você também poderá exibir a saída da tarefa em **Arquivos de saída salvos** e **Logs salvos** no [portal do Azure][portal].

![Arquivos de saída salvos e Seletores de logs salvos no portal][1]

>[AZURE.NOTE] O método de armazenamento e recuperação de arquivos discutido aqui fornece funcionalidade semelhante à forma como **Aplicativos de Lote** (agora preteridos) gerenciavam suas saídas de tarefa.

## Considerações de saída da tarefa

Ao projetar sua solução do Lote, você deve considerar vários fatores relacionados ao trabalhos e saídas de tarefas.

* **Tempo de vida de nó de computação**: os nós de computação geralmente são transitórios, especialmente em pools com dimensionamento automático habilitado. Os resultados das tarefas que são executadas em um nó estão disponíveis apenas enquanto o nó existe e apenas no tempo de retenção de arquivo definido para a tarefa. Portanto, para garantir que a saída da tarefa seja preservada, suas tarefas devem carregar os arquivos de saída para um armazenamento durável, por exemplo, o Armazenamento do Azure.

* **Armazenamento de saída**: para persistir os dados de saída de tarefa para o armazenamento durável, você pode usar o [SDK do Armazenamento do Azure](../storage/storage-dotnet-how-to-use-blobs.md) em seu código de tarefa para carregar a saída da tarefa para um contêiner de armazenamento de Blobs. Se você implementar uma convenção de nomenclatura de contêiner e arquivo, seu aplicativo cliente ou outras tarefas no trabalho poderão localizar e baixar essa saída com base na convenção.

* **Recuperação de saída**: você pode recuperar a saída da tarefa diretamente dos nós de computação em seu pool ou do Armazenamento do Azure se as tarefas persistirem sua saída. Para recuperar a saída da tarefa diretamente de um nó de computação, você precisa do nome do arquivo e do local de saída no nó. Se você persistir a saída para o Armazenamento do Azure, tarefas downstream ou o aplicativo cliente deverão ter o caminho completo do arquivo no Armazenamento do Azure para baixá-lo usando o SDK do Armazenamento do Azure.

* **Exibição da saída**: quando você navegar para uma tarefa do Lote no portal do Azure e selecionar **arquivos no nó**, verá todos os arquivos associados à tarefa, não apenas os arquivos de saída em que está interessado. Novamente, os arquivos em nós de computação estão disponíveis apenas enquanto o nó existe e apenas no tempo de retenção de arquivo definido para a tarefa. Para exibir a saída de tarefa que você persistiu no Armazenamento do Azure no portal ou um aplicativo como o [Gerenciador de Armazenamento do Azure][storage_explorer], você deve saber o local e navegar diretamente até o arquivo.

## Ajuda para saída persistente

Para ajudá-lo a persistir mais facilmente o trabalho e a saída de tarefa, a equipe do Lote definiu e implementou um conjunto de convenções de nomenclatura, bem como uma biblioteca de classes do .NET, a biblioteca [Convenções de Arquivo do Lote do Azure][nuget_package], que você pode usar em seus aplicativos do Lote. Além disso, o portal do Azure está ciente destas convenções de nomenclatura, assim, você pode encontrar facilmente os arquivos que armazenou usando a biblioteca.

## Usar a biblioteca de convenções de arquivo

[Convenções de arquivo do Lote do Azure][nuget_package] é uma biblioteca de classes .NET que seus aplicativos .NET do Lote podem usar para armazenar e recuperar saídas de tarefas no Armazenamento do Azure com facilidade. Destina-se a uso em código de tarefa e cliente - no código da tarefa para manter arquivos e no código do cliente para listá-los e recuperá-los. As tarefas também podem usar a biblioteca para recuperar as saídas de tarefas upstream, como em um cenário de [dependências de tarefas](batch-task-dependencies.md).

A biblioteca de convenções se encarrega de garantir que tarefas e contêineres de armazenamento gerem arquivos são nomeados de acordo com a convenção e sejam carregados para o local certo quando persistidos no Armazenamento do Azure. Ao recuperar saídas, você pode localizar facilmente as saídas para determinado trabalho ou tarefa listando ou recuperando as saídas por ID e finalidade, em vez de ter que saber os nomes de arquivos ou onde eles existem no Armazenamento.

Por exemplo, você pode usar a biblioteca para "listar todos os arquivos intermediários para a tarefa 7," ou "obter a visualização de miniatura para o trabalho *mymovie*", sem precisar saber os nomes de arquivo ou o local em sua conta de Armazenamento.

### Obter a biblioteca

Você pode obter a biblioteca, que contém as novas classes e estende as classes [CloudJob][net_cloudjob] e [CloudTask][net_cloudtask] com métodos novos, no [NuGet][nuget_package]. Você pode adicioná-la a seu projeto do Visual Studio usando o [Gerenciador de Pacotes da Biblioteca do NuGet][nuget_manager].

>[AZURE.TIP] Você pode encontrar o [código-fonte][github_file_conventions] da biblioteca de Convenções de Arquivo de Lote do Azure no GitHub no SDK do Microsoft Azure para o repositório do .NET.

## Requisito: conta de armazenamento vinculada

Para armazenar as saídas no armazenamento durável usando a biblioteca de convenções de arquivo e exibi-las no portal do Azure, você deve [vincular uma conta de Armazenamento do Azure](batch-application-packages.md#link-a-storage-account) à sua conta do Lote. Se você ainda não fez isso, vincule uma conta de armazenamento à sua conta do Lote usando o portal do Azure:

**Conta do Lote** folha > **Configurações** > **Conta de Armazenamento** > **Conta de Armazenamento** (Nenhum) > selecione uma conta de Armazenamento na sua assinatura

Para obter instruções mais detalhadas sobre como vincular uma conta de armazenamento, confira [Implantação de aplicativos com pacotes de aplicativos do Lote do Azure](batch-application-packages.md).

## Persistir a saída

Existem duas ações principais a serem executadas ao salvar a saída de trabalhos e tarefas com a biblioteca de convenções de arquivo: criar o contêiner de armazenamento e salvar a saída para o contêiner.

>[AZURE.WARNING] Como todas as saídas de trabalho e tarefa são armazenadas no mesmo contêiner, [limites de limitação de armazenamento](../storage/storage-performance-checklist.md#blobs) pode ser impostos se um grande número de tarefas tenta persistir arquivos ao mesmo tempo.

### Criar um contêiner de armazenamento

Antes que suas tarefas comecem a persistir a saída para o armazenamento, você deve criar um contêiner de armazenamento de blobs no qual elas carregarão sua saída. Faça isso chamando [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]. Esse método de extensão usa um objeto [CloudStorageAccount][net_cloudstorageaccount] como um parâmetro e criará um contêiner nomeado de forma que seu conteúdo seja detectável pelo portal do Azure e os métodos de recuperação discutidos posteriormente neste artigo.

Normalmente, você colocará esse código em seu aplicativo cliente – o aplicativo que cria os pools, trabalhos e tarefas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
	"myJob",
	new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
	new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### Armazenar saídas de tarefas

Agora que você preparou um contêiner no armazenamento de blobs, as tarefas podem salvar a saída no contêiner usando a classe [TaskOutputStorage][net_taskoutputstorage] encontrada na biblioteca de convenções de arquivo.

No código de tarefa, primeiro crie um objeto [TaskOutputStorage][net_taskoutputstorage]. Em seguida, quando a tarefa concluir seu trabalho, chame o método [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] para salvar sua saída no Armazenamento do Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
	linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

O parâmetro de "tipo de saída" categoriza os arquivos persistentes. Há quatro tipos [TaskOutputKind][net_taskoutputkind] predefinidos: "TaskOutput", "TaskPreview", "TaskLog" e "TaskIntermediate". Você também poderá definir tipos personalizados se eles forem úteis em seu fluxo de trabalho.

Esses tipos de saída permitem que você especifique qual tipo de saídas listar ao consultar posteriormente o Lote para obter as saídas persistentes de determinada tarefa. Em outras palavras, ao listar as saídas de uma tarefa, você pode filtrar a lista em um dos tipos de saída. Por exemplo, "Forneça a saída de *visualização* da tarefa *109*". Mais informações sobre listagem e recuperação de saídas estão disponíveis em [Recuperar saída](#retrieve-output), mais adiante no artigo.

>[AZURE.TIP] O tipo de saída também designa onde no portal do Azure um arquivo específico será exibido: arquivos categorizados por *TaskOutput* aparecerão em "Arquivos de saída de tarefa" e arquivos de *TaskLog* aparecerão em "Logs de tarefas".

### Armazenar saídas de trabalhos

Além de armazenar as saídas de tarefas, você pode armazenar as saídas associadas a um trabalho inteiro. Por exemplo, na tarefa de mesclagem de um trabalho de renderização de filme, você pode persistir o filme totalmente renderizado como uma saída de trabalho. Quando o trabalho é concluído, o aplicativo cliente pode simplesmente listar e recuperar as saídas para o trabalho e não precisa consultar as tarefas individuais.

Armazene a saída de trabalho chamando o método [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] e especifique o [JobOutputKind][net_joboutputkind] e o nome de arquivo:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Como com TaskOutputKind para saídas de tarefa, você usa o parâmetro [JobOutputKind][net_joboutputkind] para categorizar os arquivos persistente de um trabalho. Isso permite que você consulte posteriormente para (listar) um tipo específico de saída. O JobOutputKind inclui tipos de saída e visualização e dá suporte à criação de tipos personalizados.

### Armazenar logs de tarefas

Além de persistir um arquivo de armazenamento durável quando uma tarefa ou trabalho é concluído, talvez seja necessário manter os arquivos que são atualizados durante a execução de uma tarefa: arquivos de log ou `stdout.txt` e `stderr.txt`, por exemplo. Para essa finalidade, a biblioteca de Convenções de Arquivo do Lote do Azure fornece o método [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync]. Com [SaveTrackedAsync][net_savetrackedasync], você pode acompanhar alterações em um arquivo no nó (em um intervalo que você especifica) e persistir essas atualizações no Armazenamento do Azure.

No trecho de código a seguir, usamos [SaveTrackedAsync][net_savetrackedasync] para atualizar `stdout.txt` no Armazenamento do Azure a cada 15 segundos durante a execução da tarefa:

```csharp
string logFilePath = Path.Combine(
	Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

using (ITrackedSaveOperation stdout =
		taskStorage.SaveTrackedAsync(
		TaskOutputKind.TaskLog,
		logFilePath,
		"stdout.txt",
		TimeSpan.FromSeconds(15)))
{
	/* Code to process data and produce output file(s) */
}
```

`Code to process data and produce output file(s)` é simplesmente um espaço reservado para o código que sua tarefa executa normalmente. Por exemplo, você pode ter código que baixa dados do Armazenamento do Azure e executa algum tipo de transformação ou cálculo neles. A parte importante desse trecho de código é demonstrar como você pode encapsular esse código em um bloco `using` para atualizar periodicamente um arquivo com [SaveTrackedAsync][net_savetrackedasync].

>[AZURE.NOTE] Quando você habilita o rastreamento de arquivo com SaveTrackedAsync, apenas *acréscimos* ao arquivo acompanhado são persistidos no Armazenamento do Azure. Você deve usar esse método somente para acompanhar arquivos de log sem giro ou outros arquivos que são acrescentados, ou seja, os dados são adicionados ao fim do arquivo apenas quando ele é atualizado.

## Recuperar saída

Quando você recupera sua saída persistente usando a biblioteca de Convenções de Arquivo do Lote do Azure, pode fazer isso de maneira voltada para tarefas e trabalhos. Você pode solicitar a saída para determinada tarefa ou trabalho sem precisar saber o caminho no Armazenamento de blobs ou até mesmo seu nome de arquivo. Você pode simplesmente dizer: "Dê-me os arquivos de saída da tarefa *109*".

O trecho de código a seguir faz a iteração em todas as tarefas de um trabalho, imprime algumas informações sobre os arquivos de saída para a tarefa e baixa os arquivos do Armazenamento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
		task.OutputStorage(storageAccount).ListOutputs(
			TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

		output.DownloadToFileAsync(
			$"{jobId}-{output.FilePath}",
			System.IO.FileMode.Create).Wait();
    }
}
```

## Saídas de tarefas e o portal do Azure

O portal do Azure exibirá saídas de tarefas e logs que são persistidos para uma conta do Armazenamento do Azure vinculada usando as convenções de nomenclatura encontradas no [arquivo LEIAME de Convenções do Lote do Azure][github_file_conventions_readme]. Você mesmo pode implementar essas convenções em uma linguagem de sua escolha ou pode usar a biblioteca de convenções de arquivo em seus aplicativos .NET.

### Habilitar a exibição do portal

Para habilitar a exibição de suas saídas no portal, você deve atender aos seguintes requisitos:

 1. [Vincular uma conta do Armazenamento do Azure](#requirement-linked-storage-account) à sua conta do Lote.
 2. Seguir as convenções de nomenclatura predefinidas para os arquivos e contêineres de Armazenamento ao persistir saídas. Você pode encontrar a definição dessas convenções no arquivo [LEIAME][github_file_conventions_readme] da biblioteca de convenções. Se você usar a biblioteca de [Convenções de Arquivo do Lote do Azure][nuget_package] para persistir a saída, esse requisito será atendido.

### Exibir saídas no portal

Para exibir logs e saídas de tarefas no portal do Azure, navegue até a tarefa em cuja saída você está interessado e clique em **Arquivos de saída salvos** ou **Logs salvos**. Esta imagem mostra os **Arquivos de saída salvos** para a tarefa com a ID "007":

![Folha de saídas de tarefa no portal do Azure][2]

## Exemplo de código

O projeto de exemplo [PersistOutputs][github_persistoutputs] é um dos [exemplos de código do Lote do Azure][github_samples] no GitHub. Essa solução do Visual Studio 2015 demonstra como usar a biblioteca de Convenções de Arquivo de Lote do Azure para persistir a saída da tarefa para o armazenamento durável. Para executar o exemplo, siga estas etapas:

1. Abra o projeto no **Visual Studio 2015**.
2. Adicione suas **credenciais de conta** do Lote e do Armazenamento a **AccountSettings.settings** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Compile** (mas não execute) a solução. Restaure todos os pacotes NuGet, se solicitado.
4. Use o portal do Azure para carregar um [pacote de aplicativos](batch-application-packages.md) para **PersistOutputsTask**. Inclua o `PersistOutputsTask.exe` e seus assemblies dependentes no pacote .zip, defina a ID do aplicativo como "PersistOutputsTask" e a versão do pacote de aplicativos como "1.0".
5. **Inicie** (execute) o projeto **PersistOutputs**.

## Próximas etapas

### Implantação do aplicativo

O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote fornece uma maneira fácil de implantar e controlar a versão dos aplicativos que as tarefas executam em nós de computação.

### Instalação de aplicativos e preparação de dados

Confira a postagem [Instalação de aplicativos e preparação de dados em nós de computação do Lote][forum_post] no Fórum do Lote do Azure para ter uma visão geral dos vários métodos de preparação de nós para execução de tarefas. Escrita por um dos membros da equipe do Lote do Azure, essa postagem fornece uma boa descrição geral das diferentes maneiras de incluir arquivos (incluindo dados de entrada de tarefas e aplicativos) nos nós de computação, bem como algumas considerações especiais a serem levadas em conta para cada método.

[forum_post]: https://social.msdn.microsoft.com/Forums/pt-BR/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Arquivos de saída salvos e Seletores de logs salvos no portal"
[2]: ./media/batch-task-output/task-output-02.png "Folha de saídas de tarefa no portal do Azure"

<!---HONumber=AcomDC_0810_2016-->