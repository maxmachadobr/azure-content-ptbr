<properties
	pageTitle="Mover dados do Armazenamento de Blob do Azure usando conectores SSIS | Microsoft Azure"
	description="Mover dados do Armazenamento de Blob do Azure usando conectores SSIS."
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="bradsev" />

# Mover dados do Armazenamento de Blobs do Azure usando conectores SSIS

O [Feature Pack dos Serviços de Integração do SQL Server para Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornece componentes para se conectar ao Azure, transferir dados entre o Azure e fontes de dados locais e processar os dados armazenados no Azure.

Orientações sobre as tecnologias usadas para mover dados para e/ou do armazenamento de blobs do Azure estão vinculadas aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


Depois de transferirem dados locais para a nuvem, os clientes poderão acessá-los de qualquer serviço do Azure para aproveitar todo o potencial do conjunto de tecnologias do Azure. Ele pode ser usado, por exemplo, no Aprendizado de Máquina do Azure ou em um cluster HDInsight.

Normalmente, essa é a primeira etapa das instruções passo a passo de [SQL](machine-learning-data-science-process-sql-walkthrough.md) e [HDInsight](machine-learning-data-science-process-hive-walkthrough.md).

Para conferir uma discussão de cenários canônicos que usam o SSIS para atender às necessidades comerciais comuns em cenários de integração de dados híbridos, consulte o blog [Realizando mais com o Feature Pack dos serviços de integração do SQL Server para o Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx).

> [AZURE.NOTE] Para obter uma introdução completa ao armazenamento de Blobs do Azure, consulte [Noções básicas do Serviço Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Pré-requisitos

Para realizar as tarefas descritas neste artigo, você deve ter uma assinatura do Azure e configurar uma conta de armazenamento do Azure. Você precisa saber o nome da conta e a chave de conta do Armazenamento do Azure para carregar ou baixar os dados.

- Para configurar uma **assinatura do Azure**, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
- Para obter instruções sobre como criar uma **conta de armazenamento** e para obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../storage/storage-create-storage-account.md).


Para usar os **conectores SSIS**, você deverá baixar:

- **SQL Server 2014 ou 2016 Standard (ou mais recente)**: a instalação inclui os Serviços de Integração do SQL Server.
- **Feature Pack dos Serviços de Integração do Microsoft SQL Server 2014 ou 2016 para Azure**: eles podem ser baixados, respectivamente, nas páginas dos [Serviços de Integração do SQL Server 2014](http://www.microsoft.com/download/details.aspx?id=47366) e [Serviços de Integração do SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=49492).

> [AZURE.NOTE] O SSIS é instalado com o SQL Server, mas não está incluído na versão Express. Para obter informações sobre quais aplicativos estão incluídos nas várias edições do SQL Server, veja [Edições do SQL Server](http://www.microsoft.com/pt-BR/server-cloud/products/sql-server-editions/)

Para obter os materiais de treinamento sobre o SSIS, veja [Treinamento prático sobre o SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Para obter informações sobre como começar a usar o SISS para compilar pacotes ETL (extração, transformação e carregamento) simples, veja [Tutorial do SSIS: criando um pacote ETL simples](https://msdn.microsoft.com/library/ms169917.aspx).

## Baixar o conjunto de dados NYC Taxi  
O exemplo descrito aqui usa um conjunto de dados disponível publicamente, o conjunto de dados [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/). O conjunto de dados é formado por cerca de 173 milhões de corridas de táxi em NYC no ano de 2013. Há dois tipos de dados: dados sobre detalhes da corrida e dados sobre tarifas. Como há um arquivo para cada mês, temos 24 arquivos no total, cada um deles com aproximadamente 2 GB descompactados.


## Carregar dados no armazenamento de blob do Azure
Para mover dados do local para o Armazenamento de blob do Azure usando o pacote de recursos de SSIS, podemos usar uma instância da [**Tarefa de carregamento de Blob do Azure**](https://msdn.microsoft.com/library/mt146776.aspx), como mostrado abaixo:

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


Os parâmetros usados pela tarefa são descritos aqui:


Campo|Descrição|
----------------------|----------------|
**AzureStorageConnection**|Especifica um Gerenciador de Conexão de Armazenamento do Azure existente ou cria um novo, que faz referência a uma conta de armazenamento do Azure, que aponta para onde os arquivos de blob estão hospedados.|
**BlobContainer**|Especifica o nome do contêiner de blob que armazenará os arquivos carregados como blobs.|
**BlobDirectory**|Especifica o diretório de blob no qual o arquivo carregado será armazenado como um blob de blocos. O diretório de blob é uma estrutura hierárquica virtual. Se o blob já existir, ele será substituído.|
**LocalDirectory**|Especifica o diretório local que contém os arquivos a serem carregados.|
**FileName**|Especifica um filtro de nome para selecionar arquivos com o padrão de nome especificado. Por exemplo, MySheet*.xls* inclui arquivos como MySheet001.xls e MySheetABC.xlsx|
**TimeRangeFrom/TimeRangeTo**|Especifica um filtro de intervalo de tempo. Os arquivos modificados após *TimeRangeFrom* e antes de *TimeRangeTo* serão incluídos.|


> [AZURE.NOTE] As credenciais do **AzureStorageConnection** precisam estar corretas e o **BlobContainer** deve existir antes de tentar a transferência.

## Baixar dados do armazenamento de blob do Azure

Para baixar dados do armazenamento de blob do Azure no armazenamento local com SSIS, use uma instância da [Tarefa de Carregamento de Blob do Azure](https://msdn.microsoft.com/library/mt146779.aspx).

##Cenários mais avançados de SSIS-Azure
Podemos observar aqui que o pacote de recursos do SSIS permite que fluxos mais complexos sejam tratados em conjunto por tarefas de empacotamento. Por exemplo, os dados de blob poderão ser inseridos diretamente em um cluster HDInsight cuja saída pode ser baixada em um blob e em um armazenamento local. O SSIS pode executar trabalhos de Hive e Pig em um cluster HDInsight usando conectores adicionais do SSIS:

- Para executar um script Hive em um cluster HDInsight do Azure com SSIS, use a [Tarefa Hive de HDInsight do Azure](https://msdn.microsoft.com/library/mt146771.aspx).
- Para executar um script Pig em um cluster HDInsight do Azure com SSIS, use a [Tarefa Pig de HDInsight do Azure](https://msdn.microsoft.com/library/mt146781.aspx).

<!---HONumber=AcomDC_0622_2016-->