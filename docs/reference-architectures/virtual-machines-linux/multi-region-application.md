---
title: "高可用性を得るために複数の Azure リージョンで Linux VM を実行する"
description: "高可用性と回復性を得るために Azure の複数のリージョンに VM をデプロイする方法。"
author: MikeWasson
ms.date: 11/22/2016
pnp.series.title: Linux VM workloads
pnp.series.prev: n-tier
ms.openlocfilehash: 3b68f6fc79ba4b29e41ba2b04537b834bb8859b0
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="run-linux-vms-in-multiple-regions-for-high-availability"></a><span data-ttu-id="8ea57-103">高可用性を得るために複数のリージョンで Linux VM を実行する</span><span class="sxs-lookup"><span data-stu-id="8ea57-103">Run Linux VMs in multiple regions for high availability</span></span>

<span data-ttu-id="8ea57-104">このリファレンス アーキテクチャは、可用性と堅牢な災害復旧インフラストラクチャを実現するために、複数の Azure リージョンで N 層アプリケーションを実行するための一連の実証済みのプラクティスを示しています。</span><span class="sxs-lookup"><span data-stu-id="8ea57-104">This reference architecture shows a set of proven practices for running an N-tier application in multiple Azure regions, in order to achieve availability and a robust disaster recovery infrastructure.</span></span> 

<span data-ttu-id="8ea57-105">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="8ea57-105">![[0]][0]</span></span>

<span data-ttu-id="8ea57-106">*このアーキテクチャの [Visio ファイル][visio-download]をダウンロードします。*</span><span class="sxs-lookup"><span data-stu-id="8ea57-106">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="8ea57-107">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="8ea57-107">Architecture</span></span> 

<span data-ttu-id="8ea57-108">このアーキテクチャは、「[Run Windows VMs for an N-tier application](n-tier.md)」(N 層アプリケーションでの Windows VM の実行) に示されているアーキテクチャの上に構築されています。</span><span class="sxs-lookup"><span data-stu-id="8ea57-108">This architecture builds on the one shown in [Run Linux VMs for an N-tier application](n-tier.md).</span></span> 

* <span data-ttu-id="8ea57-109">**プライマリ リージョンとセカンダリ リージョン**。</span><span class="sxs-lookup"><span data-stu-id="8ea57-109">**Primary and secondary regions**.</span></span> <span data-ttu-id="8ea57-110">2 つのリージョンを使用して高可用性を実現します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-110">Use two regions to achieve higher availability.</span></span> <span data-ttu-id="8ea57-111">1 つはプライマリ リージョンであり、他方のリージョンはフェールオーバー用です。</span><span class="sxs-lookup"><span data-stu-id="8ea57-111">One is the primary region.The other region is for failover.</span></span>
* <span data-ttu-id="8ea57-112">**Azure Traffic Manager**。</span><span class="sxs-lookup"><span data-stu-id="8ea57-112">**Azure Traffic Manager**.</span></span> <span data-ttu-id="8ea57-113">[Traffic Manager][traffic-manager] は、着信要求をいずれかのリージョンにルーティングします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-113">[Traffic Manager][traffic-manager] routes incoming requests to one of the regions.</span></span> <span data-ttu-id="8ea57-114">通常の運用中は、プライマリ リージョンに要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-114">During normal operations, it routes requests to the primary region.</span></span> <span data-ttu-id="8ea57-115">そのリージョンが使用できなくなった場合、Traffic Manager はセカンダリ リージョンへのフェールオーバーを実行します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-115">If that region becomes unavailable, Traffic Manager fails over to the secondary region.</span></span> <span data-ttu-id="8ea57-116">詳細については、「[Traffic Manager の構成](#traffic-manager-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-116">For more information, see the section [Traffic Manager configuration](#traffic-manager-configuration).</span></span>
* <span data-ttu-id="8ea57-117">**リソース グループ**。</span><span class="sxs-lookup"><span data-stu-id="8ea57-117">**Resource groups**.</span></span> <span data-ttu-id="8ea57-118">プライマリ リージョン、セカンダリ リージョン、および Traffic Manager 用の個別の[リソース グループ][resource groups]を作成します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-118">Create separate [resource groups][resource groups] for the primary region, the secondary region, and for Traffic Manager.</span></span> <span data-ttu-id="8ea57-119">これにより、各リージョンをリソースの 1 つのコレクションとして柔軟に管理できます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-119">This gives you the flexibility to manage each region as a single collection of resources.</span></span> <span data-ttu-id="8ea57-120">たとえば、片方のリージョンの再デプロイを、他方のリージョンをダウンさせずに実行できます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-120">For example, you could redeploy one region, without taking down the other one.</span></span> <span data-ttu-id="8ea57-121">[リソース グループをリンク][resource-group-links]して、アプリケーション用のすべてのリソースを一覧表示するクエリを実行できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-121">[Link the resource groups][resource-group-links], so that you can run a query to list all the resources for the application.</span></span>
* <span data-ttu-id="8ea57-122">**VNets**。</span><span class="sxs-lookup"><span data-stu-id="8ea57-122">**VNets**.</span></span> <span data-ttu-id="8ea57-123">リージョンごとに個別の VNet を作成します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-123">Create a separate VNet for each region.</span></span> <span data-ttu-id="8ea57-124">アドレス空間が重複していないことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-124">Make sure the address spaces do not overlap.</span></span>
* <span data-ttu-id="8ea57-125">**Apache Cassandra**。</span><span class="sxs-lookup"><span data-stu-id="8ea57-125">**Apache Cassandra**.</span></span> <span data-ttu-id="8ea57-126">高可用性を得るために、Azure リージョンのデータセンターに Cassandra をデプロイします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-126">Deploy Cassandra in data centers across Azure regions for high availability.</span></span> <span data-ttu-id="8ea57-127">各リージョンのノードは、リージョン内の回復性を高めるために、障害ドメインとアップグレード ドメインを持つラック認識モードで構成されます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-127">Within each region, nodes are configured in rack-aware mode with fault and upgrade domains, for resiliency inside the region.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8ea57-128">推奨事項</span><span class="sxs-lookup"><span data-stu-id="8ea57-128">Recommendations</span></span>

<span data-ttu-id="8ea57-129">マルチリージョン アーキテクチャは、単一のリージョンにデプロイするよりも高い可用性を提供できます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-129">A multi-region architecture can provide higher availability than deploying to a single region.</span></span> <span data-ttu-id="8ea57-130">地域的な停止がプライマリ リージョンに影響する場合は、[Traffic Manager][traffic-manager] を使用して、セカンダリ リージョンにフェールオーバーできます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-130">If a regional outage affects the primary region, you can use [Traffic Manager][traffic-manager] to fail over to the secondary region.</span></span> <span data-ttu-id="8ea57-131">このアーキテクチャは、アプリケーションの個々のサブシステムが失敗した場合にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-131">This architecture can also help if an individual subsystem of the application fails.</span></span>

<span data-ttu-id="8ea57-132">リージョン間で高可用性を実現する一般的な方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-132">There are several general approaches to achieving high availability across regions:</span></span>   

* <span data-ttu-id="8ea57-133">アクティブ/パッシブ (ホット スタンバイ)。</span><span class="sxs-lookup"><span data-stu-id="8ea57-133">Active/passive with hot standby.</span></span> <span data-ttu-id="8ea57-134">トラフィックが片方のリージョンにルーティングされている間、他方のリージョンは、ホット スタンバイ状態で待機します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-134">Traffic goes to one region, while the other waits on hot standby.</span></span> <span data-ttu-id="8ea57-135">ホット スタンバイとは、セカンダリ リージョン内の VM が割り当て済みであり、常に実行されていることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-135">Hot standby means the VMs in the secondary region are allocated and running at all times.</span></span>
* <span data-ttu-id="8ea57-136">アクティブ/パッシブ (コールド スタンバイ)。</span><span class="sxs-lookup"><span data-stu-id="8ea57-136">Active/passive with cold standby.</span></span> <span data-ttu-id="8ea57-137">トラフィックが片方のリージョンにルーティングされている間、他方のリージョンは、コールド スタンバイ状態で待機します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-137">Traffic goes to one region, while the other waits on cold standby.</span></span> <span data-ttu-id="8ea57-138">コールド スタンバイとは、セカンダリ リージョン内の VM がフェールオーバーが必要になるまで割り当てられないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-138">Cold standby means the VMs in the secondary region are not allocated until needed for failover.</span></span> <span data-ttu-id="8ea57-139">この方法のほうが実行コストは低くなりますが、ほとんどの場合、障害発生時にオンラインになるまでの時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-139">This approach costs less to run, but will generally take longer to come online during a failure.</span></span>
* <span data-ttu-id="8ea57-140">アクティブ/アクティブ。</span><span class="sxs-lookup"><span data-stu-id="8ea57-140">Active/active.</span></span> <span data-ttu-id="8ea57-141">両方のリージョンがアクティブであり、要求はそれらの間で負荷分散されます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-141">Both regions are active, and requests are load balanced between them.</span></span> <span data-ttu-id="8ea57-142">片方のリージョンが使用できなくなった場合は、ローテーションから外されます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-142">If one region becomes unavailable, it is taken out of rotation.</span></span> 

<span data-ttu-id="8ea57-143">このアーキテクチャでは、Traffic Manager を使用してフェールオーバーで するアクティブ/パッシブ (ホット スタンバイ) に焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="8ea57-143">This architecture focuses on active/passive with hot standby, using Traffic Manager for failover.</span></span> <span data-ttu-id="8ea57-144">ホット スタンバイ用の少数の VM をデプロイした後、必要に応じてスケール アウトできることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-144">Note that you could deploy a small number of VMs for hot standby and then scale out as needed.</span></span>


### <a name="regional-pairing"></a><span data-ttu-id="8ea57-145">リージョンのペアリング</span><span class="sxs-lookup"><span data-stu-id="8ea57-145">Regional pairing</span></span>

<span data-ttu-id="8ea57-146">各 Azure リージョンは、同じ地区内の別のリージョンとペアリングされます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-146">Each Azure region is paired with another region within the same geography.</span></span> <span data-ttu-id="8ea57-147">通常は、同じリージョン ペアからリージョンを選択します (たとえば、米国東部 2 と米国中部)。</span><span class="sxs-lookup"><span data-stu-id="8ea57-147">In general, choose regions from the same regional pair (for example, East US 2 and US Central).</span></span> <span data-ttu-id="8ea57-148">これには、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-148">Benefits of doing so include:</span></span>

* <span data-ttu-id="8ea57-149">広範囲にわたる停止が発生した場合は、すべてのペアで、少なくとも 1 つのリージョンの復旧が優先的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-149">If there is a broad outage, recovery of at least one region out of every pair is prioritized.</span></span>
* <span data-ttu-id="8ea57-150">Azure システムの計画的更新は、起こり得るダウンタイムを最小限に抑えるために、ペアになっているリージョンに対して順にロールアウトされます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-150">Planned Azure system updates are rolled out to paired regions sequentially, to minimize possible downtime.</span></span>
* <span data-ttu-id="8ea57-151">ペアは、データの所在地要件を満たすために同じ地区内に所在します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-151">Pairs reside within the same geography, to meet data residency requirements.</span></span>

<span data-ttu-id="8ea57-152">ただし、両方のリージョンでアプリケーションに必要なすべての Azure サービスがサポートされていることを確認してください ([リージョン別サービス][services-by-region]に関する記事を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="8ea57-152">However, make sure that both regions support all of the Azure services needed for your application (see [Services by region][services-by-region]).</span></span> <span data-ttu-id="8ea57-153">リージョン ペアの詳細については、「[ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン][regional-pairs]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-153">For more information about regional pairs, see [Business continuity and disaster recovery (BCDR): Azure Paired Regions][regional-pairs].</span></span>

### <a name="traffic-manager-configuration"></a><span data-ttu-id="8ea57-154">Traffic Manager の構成</span><span class="sxs-lookup"><span data-stu-id="8ea57-154">Traffic Manager configuration</span></span>

<span data-ttu-id="8ea57-155">Traffic Manager を構成するときは、次の点を検討してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-155">Consider the following points when configuring Traffic Manager:</span></span>

* <span data-ttu-id="8ea57-156">**ルーティング**。</span><span class="sxs-lookup"><span data-stu-id="8ea57-156">**Routing**.</span></span> <span data-ttu-id="8ea57-157">Traffic Manager は、さまざまな[ルーティング アルゴリズム][tm-routing]をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8ea57-157">Traffic Manager supports several [routing algorithms][tm-routing].</span></span> <span data-ttu-id="8ea57-158">この記事で説明するシナリオでは、"*優先度による*" ルーティング (旧称 "*フェールオーバー*" ルーティング) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-158">For the scenario described in this article, use *priority* routing (formerly called *failover* routing).</span></span> <span data-ttu-id="8ea57-159">この設定では、プライマリ リージョンが到達不能にならない限り、Traffic Manager はプライマリ リージョンにすべての要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-159">With this setting, Traffic Manager sends all requests to the primary region, unless the primary region becomes unreachable.</span></span> <span data-ttu-id="8ea57-160">到達不能になった時点で、セカンダリ リージョンに自動的にフェールオーバーします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-160">At that point, it automatically fails over to the secondary region.</span></span> <span data-ttu-id="8ea57-161">[フェールオーバーのルーティング方法の構成][tm-configure-failover]に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-161">See [Configure Failover routing method][tm-configure-failover].</span></span>
* <span data-ttu-id="8ea57-162">**正常性プローブ**。</span><span class="sxs-lookup"><span data-stu-id="8ea57-162">**Health probe**.</span></span> <span data-ttu-id="8ea57-163">Traffic Manager は、HTTP (または HTTPS) [プローブ][tm-monitoring]を使用して、各リージョンの可用性を監視します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-163">Traffic Manager uses an HTTP (or HTTPS) [probe][tm-monitoring] to monitor the availability of each region.</span></span> <span data-ttu-id="8ea57-164">プローブは、特定の URL パスの HTTP 200 応答をチェックします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-164">The probe checks for an HTTP 200 response for a specified URL path.</span></span> <span data-ttu-id="8ea57-165">ベスト プラクティスとして、アプリケーションの全体的な正常性を報告するエンドポイントを作成し、そのエンドポイントを正常性プローブ用に使用します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-165">As a best practice, create an endpoint that reports the overall health of the application, and use this endpoint for the health probe.</span></span> <span data-ttu-id="8ea57-166">これを行わなかった場合、プローブは、アプリケーションの重要な部分で実際には障害が発生しているにもかかわらず、エンドポイントが正常であると報告する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-166">Otherwise, the probe might report a healthy endpoint when critical parts of the application are actually failing.</span></span> <span data-ttu-id="8ea57-167">詳細については、[正常性エンドポイント監視パターン][health-endpoint-monitoring-pattern]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-167">For more information, see [Health Endpoint Monitoring Pattern][health-endpoint-monitoring-pattern].</span></span>

<span data-ttu-id="8ea57-168">Traffic Manager がフェールオーバーを実行すると、クライアントがアプリケーションに到達できない時間が発生します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-168">When Traffic Manager fails over there is a period of time when clients cannot reach the application.</span></span> <span data-ttu-id="8ea57-169">この持続時間は、次の要因に影響されます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-169">The duration is affected by the following factors:</span></span>

* <span data-ttu-id="8ea57-170">正常性プローブが、プライマリ リージョンが到達不能になっていることを検出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-170">The health probe must detect that the primary region has become unreachable.</span></span>
* <span data-ttu-id="8ea57-171">DNS サーバーが、IP アドレスのキャッシュされた DNS レコードを更新する必要があります。これは DNS 有効期限 (TTL) に依存します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-171">DNS servers must update the cached DNS records for the IP address, which depends on the DNS time-to-live (TTL).</span></span> <span data-ttu-id="8ea57-172">TTL の既定値は 300 秒 (5 分) ですが、この値は、Traffic Manager プロファイルを作成するときに構成できます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-172">The default TTL is 300 seconds (5 minutes), but you can configure this value when you create the Traffic Manager profile.</span></span>

<span data-ttu-id="8ea57-173">詳細については、「[Traffic Manager の監視について][tm-monitoring]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-173">For details, see [About Traffic Manager Monitoring][tm-monitoring].</span></span>

<span data-ttu-id="8ea57-174">Traffic Manager でフェールオーバーを実行する場合は、自動フェールバックを実装するのではなく、手動でフェールバックを実行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-174">If Traffic Manager fails over, we recommend performing a manual failback rather than implementing an automatic failback.</span></span> <span data-ttu-id="8ea57-175">これを行わなかった場合、リージョン間でアプリケーションが切り替わる状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-175">Otherwise, you can create a situation where the application flips back and forth between regions.</span></span> <span data-ttu-id="8ea57-176">フェールバックする前に、すべてのアプリケーション サブシステムが正常であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-176">Verify that all application subsystems are healthy before failing back.</span></span>

<span data-ttu-id="8ea57-177">Traffic Manager は、既定では自動的にフェールバックすることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-177">Note that Traffic Manager automatically fails back by default.</span></span> <span data-ttu-id="8ea57-178">これが起こらないようにするには、フェールオーバー イベントの後、手動でプライマリ リージョンの優先度を下げます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-178">To prevent this, manually lower the priority of the primary region after a failover event.</span></span> <span data-ttu-id="8ea57-179">たとえば、プライマリ リージョンの優先度は 1、セカンダリ リージョンの優先度は 2 であるとします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-179">For example, suppose the primary region is priority 1 and the secondary is priority 2.</span></span> <span data-ttu-id="8ea57-180">フェールオーバーした後、プライマリ リージョンの優先度を 3 に設定して、自動フェールバックが起こらないにします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-180">After a failover, set the primary region to priority 3, to prevent automatic failback.</span></span> <span data-ttu-id="8ea57-181">元に戻す準備ができたら、優先度を 1 に更新します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-181">When you are ready to switch back, update the priority to 1.</span></span>

<span data-ttu-id="8ea57-182">次の [Azure CLI][install-azure-cli] コマンドは、優先度を更新します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-182">The following [Azure CLI][install-azure-cli] command updates the priority:</span></span>

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

<span data-ttu-id="8ea57-183">別の方法は、フェールバックの準備ができるまで、エンドポイントを一時的に無効にすることです。</span><span class="sxs-lookup"><span data-stu-id="8ea57-183">Another approach is to temporarily disable the endpoint until you are ready to fail back:</span></span>

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```    

<span data-ttu-id="8ea57-184">フェールオーバーの原因によっては、リソースをリージョン内に再デプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-184">Depending on the cause of a failover, you might need to redeploy the resources within a region.</span></span> <span data-ttu-id="8ea57-185">フェールバックする前に、運用準備テストを実行します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-185">Before failing back, perform an operational readiness test.</span></span> <span data-ttu-id="8ea57-186">このテストでは、以下のような点を検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-186">The test should verify things like:</span></span>

* <span data-ttu-id="8ea57-187">VM が正しく構成されている </span><span class="sxs-lookup"><span data-stu-id="8ea57-187">VMs are configured correctly.</span></span> <span data-ttu-id="8ea57-188">(すべての必要なソフトウェアがインストールされていること、IIS が実行されていることなど)。</span><span class="sxs-lookup"><span data-stu-id="8ea57-188">(All required software is installed, IIS is running, and so on.)</span></span>
* <span data-ttu-id="8ea57-189">アプリケーションのサブシステムが正常である。</span><span class="sxs-lookup"><span data-stu-id="8ea57-189">Application subsystems are healthy.</span></span>
* <span data-ttu-id="8ea57-190">機能テスト </span><span class="sxs-lookup"><span data-stu-id="8ea57-190">Functional testing.</span></span> <span data-ttu-id="8ea57-191">(たとえば、Web 層からデータベース層に到達可能である)。</span><span class="sxs-lookup"><span data-stu-id="8ea57-191">(For example, the database tier is reachable from the web tier.)</span></span>

### <a name="cassandra-deployment-across-multiple-regions"></a><span data-ttu-id="8ea57-192">複数のリージョンでの Cassandra のデプロイ</span><span class="sxs-lookup"><span data-stu-id="8ea57-192">Cassandra deployment across multiple regions</span></span>

<span data-ttu-id="8ea57-193">Cassandra データ センターとは、レプリケーションとワークロードを分離するためにクラスター内にまとめて構成されている関連性のあるデータ ノードのグループです。</span><span class="sxs-lookup"><span data-stu-id="8ea57-193">Cassandra data centers are a group of related data nodes that are configured together within a cluster for replication and workload segregation.</span></span>

<span data-ttu-id="8ea57-194">実稼働環境では [DataStax Enterprise][datastax] の使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-194">We recommend [DataStax Enterprise][datastax] for production use.</span></span> <span data-ttu-id="8ea57-195">Azure での DataStax の実行の詳細については、「[DataStax Enterprise Deployment Guide for Azure][cassandra-in-azure]」(Azure 用の DataStax Enterprise Deployment ガイド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-195">For more information on running DataStax in Azure, see [DataStax Enterprise Deployment Guide for Azure][cassandra-in-azure].</span></span> <span data-ttu-id="8ea57-196">すべての Cassandra エディションに次の一般的な推奨事項が適用されます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-196">The following general recommendations apply to any Cassandra edition:</span></span> 

* <span data-ttu-id="8ea57-197">各ノードにパブリック IP アドレスを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-197">Assign a public IP address to each node.</span></span> <span data-ttu-id="8ea57-198">これにより、クラスターは、Azure バックボーン インフラストラクチャを使用してリージョン間で通信でき、高いスループットを低コストで提供します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-198">This enables the clusters to communicate across regions using the Azure backbone infrastructure, providing high throughput at low cost.</span></span>
* <span data-ttu-id="8ea57-199">適切なファイアウォールとネットワーク セキュリティ グループ (NSG) 構成を使用してノードをセキュリティ保護して、クライアントと他のクラスター ノードも含めて、既知のホストに対するトラフィックのみを許可します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-199">Secure nodes using the appropriate firewall and network security group (NSG) configurations, allowing traffic only to and from known hosts, including clients and other cluster nodes.</span></span> <span data-ttu-id="8ea57-200">Cassandra は、通信、OpsCenter、Spark などのために、異なるポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-200">Note that Cassandra uses different ports for communication, OpsCenter, Spark, and so forth.</span></span> <span data-ttu-id="8ea57-201">Cassandra のポートの使用方法については、「[Configuring firewall port access][cassandra-ports]」(ファイアウォールのポートのアクセスの構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-201">For port usage in Cassandra, see [Configuring firewall port access][cassandra-ports].</span></span>
* <span data-ttu-id="8ea57-202">すべての[クライアントからノードへ][ssl-client-node]の通信と[ノードからノードへ][ssl-node-node]の通信で、SSL 暗号化を使用します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-202">Use SSL encryption for all [client-to-node][ssl-client-node] and [node-to-node][ssl-node-node] communications.</span></span>
* <span data-ttu-id="8ea57-203">リージョン内では、[Cassandra に関する推奨事項](n-tier.md#cassandra)のガイドラインに従います。</span><span class="sxs-lookup"><span data-stu-id="8ea57-203">Within a region, follow the guidelines in [Cassandra recommendations](n-tier.md#cassandra).</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="8ea57-204">可用性に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8ea57-204">Availability considerations</span></span>

<span data-ttu-id="8ea57-205">複雑な N 層アプリケーションでは、アプリケーション全体をセカンダリ リージョンにレプリケートする必要はない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-205">With a complex N-tier app, you may not need to replicate the entire application in the secondary region.</span></span> <span data-ttu-id="8ea57-206">代わりに、ビジネス継続性をサポートするために必要な重要なサブシステムのみをレプリケートできます。</span><span class="sxs-lookup"><span data-stu-id="8ea57-206">Instead, you might just replicate a critical subsystem that is needed to support business continuity.</span></span>

<span data-ttu-id="8ea57-207">Traffic Manager は、システムの障害ポイントになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-207">Traffic Manager is a possible failure point in the system.</span></span> <span data-ttu-id="8ea57-208">Traffic Manager サービスが失敗すると、クライアントは、ダウンタイム中はアプリケーションにアクセスできなくなります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-208">If the Traffic Manager service fails, clients cannot access your application during the downtime.</span></span> <span data-ttu-id="8ea57-209">「[Traffic Manager の SLA][tm-sla]」を確認して、Traffic Manager の使用だけで高可用性のビジネス要件が満たされるかどうかを確かめてください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-209">Review the [Traffic Manager SLA][tm-sla], and determine whether using Traffic Manager alone meets your business requirements for high availability.</span></span> <span data-ttu-id="8ea57-210">満たされない場合は、フェールバックとして別のトラフィック管理ソリューションを追加することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-210">If not, consider adding another traffic management solution as a failback.</span></span> <span data-ttu-id="8ea57-211">Azure Traffic Manager サービスで障害が発生した場合は、他のトラフィック管理サービスを参照するように、DNS の CNAME レコードを変更します </span><span class="sxs-lookup"><span data-stu-id="8ea57-211">If the Azure Traffic Manager service fails, change your CNAME records in DNS to point to the other traffic management service.</span></span> <span data-ttu-id="8ea57-212">(この手順は手動で実行する必要があり、DNS の変更が反映されるまでアプリケーションを使用することはできません)。</span><span class="sxs-lookup"><span data-stu-id="8ea57-212">(This step must be performed manually, and your application will be unavailable until the DNS changes are propagated.)</span></span>

<span data-ttu-id="8ea57-213">Cassandra クラスターのために検討するフェールオーバー シナリオは、アプリケーションで使用される一貫性レベルと使用されるレプリカの数によって異なります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-213">For the Cassandra cluster, the failover scenarios to consider depend on the consistency levels used by the application, as well as the number of replicas used.</span></span> <span data-ttu-id="8ea57-214">Cassandra での一貫性レベルと使用については、「[Configuring data consistency][cassandra-consistency]」(データの一貫性の構成) と「[Cassandra: How many nodes are talked to with Quorum?][cassandra-consistency-usage]」(Cassandra: Quorum を使用してトークできるノードの数) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-214">For consistency levels and usage in Cassandra, see [Configuring data consistency][cassandra-consistency] and [Cassandra: How many nodes are talked to with Quorum?][cassandra-consistency-usage]</span></span> <span data-ttu-id="8ea57-215">Cassandra でのデータの可用性は、アプリケーションで使用される一貫性レベルとレプリケーション メカニズムによって決まります。</span><span class="sxs-lookup"><span data-stu-id="8ea57-215">Data availability in Cassandra is determined by the consistency level used by the application and the replication mechanism.</span></span> <span data-ttu-id="8ea57-216">Cassandra のレプリケーションについては、「[Data Replication in NoSQL Databases Explained][cassandra-replication]」(NoSQL Database でのデータレプリケーションの説明) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ea57-216">For replication in Cassandra, see [Data Replication in NoSQL Databases Explained][cassandra-replication].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="8ea57-217">管理容易性に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8ea57-217">Manageability considerations</span></span>

<span data-ttu-id="8ea57-218">デプロイを更新するときは、一度に 1 つのリージョンを更新することで、アプリケーションの不適切な構成やエラーによってグローバル エラーが発生する機会を減らします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-218">When you update your deployment, update one region at a time to reduce the chance of a global failure from an incorrect configuration or an error in the application.</span></span>

<span data-ttu-id="8ea57-219">システムのエラーに対する回復性をテストします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-219">Test the resiliency of the system to failures.</span></span> <span data-ttu-id="8ea57-220">テストされる一般的な障害シナリオを次に示します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-220">Here are some common failure scenarios to test:</span></span>

* <span data-ttu-id="8ea57-221">VM インスタンスのシャットダウン。</span><span class="sxs-lookup"><span data-stu-id="8ea57-221">Shut down VM instances.</span></span>
* <span data-ttu-id="8ea57-222">CPU やメモリなどのリソースへの負荷。</span><span class="sxs-lookup"><span data-stu-id="8ea57-222">Pressure resources such as CPU and memory.</span></span>
* <span data-ttu-id="8ea57-223">ネットワークの切断/遅延。</span><span class="sxs-lookup"><span data-stu-id="8ea57-223">Disconnect/delay network.</span></span>
* <span data-ttu-id="8ea57-224">プロセスのクラッシュ。</span><span class="sxs-lookup"><span data-stu-id="8ea57-224">Crash processes.</span></span>
* <span data-ttu-id="8ea57-225">証明書の期限切れ。</span><span class="sxs-lookup"><span data-stu-id="8ea57-225">Expire certificates.</span></span>
* <span data-ttu-id="8ea57-226">ハードウェア障害のシミュレート。</span><span class="sxs-lookup"><span data-stu-id="8ea57-226">Simulate hardware faults.</span></span>
* <span data-ttu-id="8ea57-227">ドメイン コントローラー上の DNS サービスのシャットダウン。</span><span class="sxs-lookup"><span data-stu-id="8ea57-227">Shut down the DNS service on the domain controllers.</span></span>

<span data-ttu-id="8ea57-228">回復時間を測定し、ビジネス要件を満たしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8ea57-228">Measure the recovery times and verify they meet your business requirements.</span></span> <span data-ttu-id="8ea57-229">障害モードの組み合わせもテストします。</span><span class="sxs-lookup"><span data-stu-id="8ea57-229">Test combinations of failure modes, as well.</span></span>


<!-- Links -->
[hybrid-vpn]: ../hybrid-networking/vpn.md

[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: https://docs.datastax.com/en/datastax_enterprise/5.0/datastax_enterprise/sec/configFirewallPorts.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[install-azure-cli]: /azure/xplat-cli-install
[regional-pairs]: /azure/best-practices-availability-paired-regions
[resource groups]: /azure/azure-resource-manager/resource-group-overview
[resource-group-links]: /azure/resource-group-link-resources
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
[tablediff]: https://msdn.microsoft.com/library/ms162843.aspx
[tm-configure-failover]: /azure/traffic-manager/traffic-manager-configure-failover-routing-method
[tm-monitoring]: /azure/traffic-manager/traffic-manager-monitoring
[tm-routing]: /azure/traffic-manager/traffic-manager-routing-methods
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[wsfc]: https://msdn.microsoft.com/library/hh270278.aspx
[0]: ./images/multi-region-application-diagram.png "Azure の N 層アプリケーション用の可用性の高いネットワーク アーキテクチャ"