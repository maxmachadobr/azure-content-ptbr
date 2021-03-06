<properties
	pageTitle="Diretrizes de conjuntos de disponibilidade | Microsoft Azure"
	description="Saiba mais sobre as principais diretrizes de design e implementação para a implantação de conjuntos de disponibilidade em serviços de infraestrutura do Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="iainfou"/>

# Diretrizes de conjuntos de disponibilidade

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artigo concentra-se em compreender as etapas de planejamento necessárias para conjuntos de disponibilidade para garantir que seus aplicativos permaneçam acessíveis durante eventos planejados ou não planejados.

## Diretrizes de implementação para conjuntos de disponibilidade

Decisões:

- Quantos conjuntos de disponibilidade são necessários para as várias funções e camadas da infraestrutura do seu aplicativo?

Tarefas:

- Defina o número de VMs em cada camada de aplicativo que você precisa.
- Determine se você precisa ajustar o número de falhas ou atualizar domínios a serem usados para seu aplicativo.
- Definir os conjuntos de disponibilidade necessários usando sua convenção de nomenclatura e quais VMs residirão neles. Uma VM pode residir apenas em um conjunto de disponibilidade.

## Conjuntos de disponibilidade

No Azure, VMs (máquinas virtuais) podem ser depositadas em um agrupamento lógico denominado um conjunto de disponibilidade. Quando você cria máquinas virtuais dentro de um conjunto de disponibilidade, a plataforma do Azure distribui o posicionamento dessas VMs em toda a infraestrutura subjacente. Caso ocorra um evento de manutenção planejada para a plataforma Azure ou uma falha de infraestrutura/hardware subjacente, o uso de conjuntos de disponibilidade garante que pelo menos uma VM permaneça em execução.

Como prática recomendada, os aplicativos não devem residir em uma única VM. Um conjunto de disponibilidade que contém uma única VM não obtém nenhuma proteção contra eventos planejados ou não planejados na plataforma Azure. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) requer duas ou mais VMs dentro de um conjunto de disponibilidade para permitir a distribuição das VMs em toda a infraestrutura subjacente.

A infraestrutura subjacente no Azure é dividida em atualizar domínios e domínios de falha. Esses domínios são definidos por quais hosts compartilharão um ciclo de atualização comum, ou compartilharão infraestrutura física semelhante, como energia e rede. O Azure distribuirá automaticamente suas VMs dentro de um conjunto de disponibilidade entre domínios para manter a disponibilidade e tolerância a falhas. Dependendo do tamanho do seu aplicativo e o número de VMs em um conjunto de disponibilidade, você pode ajustar o número de domínios que deseja usar. Você pode ler mais sobre [Gerenciar a disponibilidade e o uso dos domínios de atualização e de falha](virtual-machines-windows-manage-availability.md).

Ao projetar sua infraestrutura de aplicativos, você também deve planejar as camadas de aplicativo que usará. Agrupe VMs que têm a mesma finalidade em conjuntos de disponibilidade, como um conjunto de disponibilidade para suas VMs de front-end executando IIS. Crie um conjunto de disponibilidade separado para suas VMs de back-end executando o SQL Server. O objetivo é garantir que cada componente do aplicativo seja protegido por um conjunto de disponibilidade e pelo menos uma instância permanecerá sempre em execução.

Balanceadores de carga podem ser utilizados antes de cada camada de aplicativo para trabalhar junto com um conjunto de disponibilidade e certificar-se de que o tráfego sempre possa ser roteado para uma instância em execução. Sem um balanceador de carga, suas VMs podem continuar em execução durante manutenções planejadas e não planejadas, mas os usuários finais não poderá resolvê-los se a VM primária não estiver disponível.


## Próximas etapas
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0706_2016-->