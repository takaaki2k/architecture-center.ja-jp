---
title: サーバーレス Web アプリケーション
description: サーバーレス Web アプリケーションと Web API を示す参照アーキテクチャ
author: MikeWasson
ms.date: 10/16/2018
ms.openlocfilehash: c2b46a60a57381ac3fd3f77cffe53b2dab2dacd6
ms.sourcegitcommit: 113a7248b9793c670b0f2d4278d30ad8616abe6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49349957"
---
# <a name="serverless-web-application"></a><span data-ttu-id="8c2a4-103">サーバーレス Web アプリケーション</span><span class="sxs-lookup"><span data-stu-id="8c2a4-103">Serverless web application</span></span> 

<span data-ttu-id="8c2a4-104">この参照アーキテクチャは、サーバーレス Web アプリケーションを示しています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-104">This reference architecture shows a serverless web application.</span></span> <span data-ttu-id="8c2a4-105">このアプリケーションでは Azure Blob Storage から静的コンテンツを提供し、Azure Functions を使用して API が実装されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-105">The application serves static content from Azure Blob Storage, and implements an API using Azure Functions.</span></span> <span data-ttu-id="8c2a4-106">API は Cosmos DB からデータを読み取り、結果を Web アプリに返します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-106">The API reads data from Cosmos DB and returns the results to the web app.</span></span> <span data-ttu-id="8c2a4-107">このアーキテクチャのリファレンス実装は、[GitHub][github] で入手できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-107">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![](./_images/serverless-web-app.png)
 
<span data-ttu-id="8c2a4-108">サーバーレスという言葉は 2 つの異なる意味を持ちますが、この 2 つの意味は関連しています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-108">The term serverless has two distinct but related meanings:</span></span>

- <span data-ttu-id="8c2a4-109">**サービスとしてのバックエンド** (BaaS)。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-109">**Backend as a service** (BaaS).</span></span> <span data-ttu-id="8c2a4-110">データベースやストレージなどのバックエンド クラウド サービスには、クライアント アプリケーションがこれらのサービスに直接接続できるようにする API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-110">Backend cloud services, such as databases and storage, provide APIs that enable client applications to connect directly to these services.</span></span> 
- <span data-ttu-id="8c2a4-111">**サービスとしての関数** (FaaS)。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-111">**Functions as a service** (FaaS).</span></span> <span data-ttu-id="8c2a4-112">このモデルでは、"関数" はクラウドにデプロイされるひとまとまりのコードで、コードを実行するサーバーが完全に抽象化されたホスティング環境内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-112">In this model, a "function" is a piece of code that is deployed to the cloud and runs inside a hosting environment that completely abstracts the servers that run the code.</span></span> 

<span data-ttu-id="8c2a4-113">両方の定義に共通しているのは、開発者と DevOps スタッフが、サーバーをデプロイ、構成、または管理する必要がないというアイデアです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-113">Both definitions have in common the idea that developers and DevOps personnel don't need to deploy, configure, or manage servers.</span></span> <span data-ttu-id="8c2a4-114">Azure Blob Storage からの Web コンテンツ提供は BaaS の一例ですが、この参照アーキテクチャでは、Azure Functions を使用した FaaS に重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-114">This reference architecture focuses on FaaS using Azure Functions, although serving web content from Azure Blob Storage is an example of BaaS.</span></span> <span data-ttu-id="8c2a4-115">FaaS の重要な特性をいくつか次に示します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-115">Some important characteristics of FaaS are:</span></span>

1. <span data-ttu-id="8c2a4-116">コンピューティング リソースが、プラットフォームによって必要に応じて動的に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-116">Compute resources are allocated dynamically as needed by the platform.</span></span>
1. <span data-ttu-id="8c2a4-117">使用量ベースの価格: ご自身のコードの実行に使用されたコンピューティング リソースに対してのみ課金されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-117">Consumption-based pricing: You are charged only for the compute resources used to execute your code.</span></span>
1. <span data-ttu-id="8c2a4-118">コンピューティング リソースがトラフィックに基づいてオンデマンドでスケーリングされます。開発者が構成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-118">The compute resources scale on demand based on traffic, without the developer needing to do any configuration.</span></span>

<span data-ttu-id="8c2a4-119">HTTP 要求やメッセージがキューに到着するなど、外部トリガーが発生すると、関数が実行されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-119">Functions are executed when an external trigger occurs, such as an HTTP request or a message arriving on a queue.</span></span> <span data-ttu-id="8c2a4-120">このため、サーバーレス アーキテクチャにとって、[イベント ドリブン アーキテクチャのスタイル][event-driven]が自然になります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-120">This makes an [event-driven architecture style][event-driven] natural for serverless architectures.</span></span> <span data-ttu-id="8c2a4-121">アーキテクチャでのコンポーネント間の動作を調整するには、メッセージ ブローカーまたはパブリッシュ/サブスクライブ パターンの使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-121">To coordinate work between components in the architecture, consider using message brokers or pub/sub patterns.</span></span> <span data-ttu-id="8c2a4-122">Azure でのメッセージング テクノロジの選択に関するヘルプについては、「[メッセージを配信する Azure サービスの選択][azure-messaging]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-122">For help choosing between messaging technologies in Azure, see [Choose between Azure services that deliver messages][azure-messaging].</span></span>

## <a name="architecture"></a><span data-ttu-id="8c2a4-123">アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="8c2a4-123">Architecture</span></span>
<span data-ttu-id="8c2a4-124">アーキテクチャは、次のコンポーネントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-124">The architecture consists of the following components.</span></span>

<span data-ttu-id="8c2a4-125">**Blob Storage**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-125">**Blob Storage**.</span></span> <span data-ttu-id="8c2a4-126">HTML、CSS、JavaScript ファイルなどの静的 Web コンテンツは Azure Blob Storage に格納され、[静的 Web サイトのホスティング][static-hosting]を使用してクライアントに提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-126">Static web content, such as HTML, CSS, and JavaScript files, are stored in Azure Blob Storage and served to clients by using [static website hosting][static-hosting].</span></span> <span data-ttu-id="8c2a4-127">動的な対話はすべて、バックエンド API を呼び出す JavaScript コードを介して行われます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-127">All dynamic interaction happens through JavaScript code making calls to the backend APIs.</span></span> <span data-ttu-id="8c2a4-128">Web ページをレンダリングするサーバー側のコードはありません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-128">There is no server-side code to render the web page.</span></span> <span data-ttu-id="8c2a4-129">静的な Web サイトのホスティングでは、インデックス ドキュメントとカスタム 404 エラー ページがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-129">Static website hosting supports index documents and custom 404 error pages.</span></span>

> [!NOTE]
> <span data-ttu-id="8c2a4-130">静的 Web サイトのホスティングは現在[プレビュー][static-hosting-preview]の段階です。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-130">Static website hosting is currently in [preview][static-hosting-preview].</span></span>

<span data-ttu-id="8c2a4-131">**CDN**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-131">**CDN**.</span></span> <span data-ttu-id="8c2a4-132">[Azure Content Delivery Network][cdn] (CDN) を使用して、待機時間を短縮してコンテンツを高速に配信できるようにコンテンツをキャッシュし、HTTPS エンドポイントを提供します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-132">Use [Azure Content Delivery Network][cdn] (CDN) to cache content for lower latency and faster delivery of content, as well as providing an HTTPS endpoint.</span></span>

<span data-ttu-id="8c2a4-133">**Function App**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-133">**Function Apps**.</span></span> <span data-ttu-id="8c2a4-134">[Azure Functions][functions] はサーバーレス コンピューティングの 1 つのオプションです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-134">[Azure Functions][functions] is a serverless compute option.</span></span> <span data-ttu-id="8c2a4-135">ひとまとまりのコード ("関数") がトリガーによって呼び出されるイベント ドリブン モデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-135">It uses an event-driven model, where a piece of code (a "function") is invoked by a trigger.</span></span> <span data-ttu-id="8c2a4-136">このアーキテクチャでは、関数は、クライアントが HTTP 要求を行うと呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-136">In this architecture, the function is invoked when a client makes an HTTP request.</span></span> <span data-ttu-id="8c2a4-137">要求は、以下で説明するように、常に API ゲートウェイ経由でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-137">The request is always routed through an API gateway, described below.</span></span>

<span data-ttu-id="8c2a4-138">**API Management**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-138">**API Management**.</span></span> <span data-ttu-id="8c2a4-139">[API Management][apim] は、HTTP 関数の前に配置される API ゲートウェイを提供します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-139">[API Management][apim] provides a API gateway that sits in front of the HTTP function.</span></span> <span data-ttu-id="8c2a4-140">API Management を使用すると、クライアント アプリケーションで使用される API を発行および管理できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-140">You can use API Management to publish and manage APIs used by client applications.</span></span> <span data-ttu-id="8c2a4-141">ゲートウェイは、バックエンド API からフロントエンド アプリケーションを分離するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-141">Using a gateway helps to decouple the front-end application from the back-end APIs.</span></span> <span data-ttu-id="8c2a4-142">たとえば、API Management では、URL の再書き込み、バックエンドに到達する前の要求の変換、要求または応答ヘッダーの設定などを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-142">For example, API Management can rewrite URLs, transform requests before they reach the backend, set request or response headers, and so forth.</span></span>

<span data-ttu-id="8c2a4-143">API Management を使って、複数のサービスにまたがる、次のような機能を実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-143">API Management can also be used to implement cross-cutting concerns such as:</span></span>

- <span data-ttu-id="8c2a4-144">使用量クォータとレート制限の強制</span><span class="sxs-lookup"><span data-stu-id="8c2a4-144">Enforcing usage quotas and rate limits</span></span>
- <span data-ttu-id="8c2a4-145">認証用の OAuth トークンの検証</span><span class="sxs-lookup"><span data-stu-id="8c2a4-145">Validating OAuth tokens for authentication</span></span>
- <span data-ttu-id="8c2a4-146">クロス オリジン要求 (CORS) の有効化</span><span class="sxs-lookup"><span data-stu-id="8c2a4-146">Enabling cross-origin requests (CORS)</span></span>
- <span data-ttu-id="8c2a4-147">応答のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="8c2a4-147">Caching responses</span></span>
- <span data-ttu-id="8c2a4-148">要求の監視とログ記録</span><span class="sxs-lookup"><span data-stu-id="8c2a4-148">Monitoring and logging requests</span></span>  

<span data-ttu-id="8c2a4-149">API Management によって提供される一部の機能が不要の場合は、別の選択肢として[関数プロキシ][functions-proxy]を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-149">If you don't need all of the functionality provided by API Management, another option is to use [Functions Proxies][functions-proxy].</span></span> <span data-ttu-id="8c2a4-150">Azure Functions のこの機能を使用すると、バックエンド関数へのルートを作成することで、複数の関数アプリに対して 1 つの API サーフェスを定義できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-150">This feature of Azure Functions lets you define a single API surface for multiple function apps, by creating routes to back-end functions.</span></span> <span data-ttu-id="8c2a4-151">関数プロキシによって、HTTP 要求および応答で制限付き変換を実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-151">Function proxies can also perform limited transformations on the HTTP request and response.</span></span> <span data-ttu-id="8c2a4-152">ただし、API Management ほど豊富なポリシー ベース機能は用意されていません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-152">However, they don't provide the same rich policy-based capabilities of API Management.</span></span>

<span data-ttu-id="8c2a4-153">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-153">**Cosmos DB**.</span></span> <span data-ttu-id="8c2a4-154">[Cosmos DB][cosmosdb] は、マルチモデル データベース サービスです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-154">[Cosmos DB][cosmosdb] is a multi-model database  service.</span></span> <span data-ttu-id="8c2a4-155">このシナリオでは、関数アプリケーションは、クライアントからの HTTP GET 要求に応答して Cosmos DB からドキュメントをフェッチします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-155">For this scenario, the function application fetches documents from Cosmos DB in response to HTTP GET requests from the client.</span></span>

<span data-ttu-id="8c2a4-156">**Azure Active Directory** (Azure AD)。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-156">**Azure Active Directory** (Azure AD).</span></span> <span data-ttu-id="8c2a4-157">ユーザーは、自身の Azure AD 資格情報を使用して Web アプリケーションにサインインします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-157">Users sign into the web application by using their Azure AD credentials.</span></span> <span data-ttu-id="8c2a4-158">Azure AD から API のアクセス トークンが返されます。Web アプリケーションはこれを使って API 要求を認証します (「[認証](#authentication)」を参照)。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-158">Azure AD returns an access token for the API, which the web application uses to authenticate API requests (see [Authentication](#authentication)).</span></span>

<span data-ttu-id="8c2a4-159">**Azure Monitor**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-159">**Azure Monitor**.</span></span> <span data-ttu-id="8c2a4-160">[Monitor][monitor] は、ソリューションにデプロイされた Azure サービスに関するパフォーマンス メトリックを収集します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-160">[Monitor][monitor] collects performance metrics about the Azure services deployed in the solution.</span></span> <span data-ttu-id="8c2a4-161">ダッシュボードでこれらを視覚化することで、ソリューションの正常性を把握できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-161">By visualizing these in a dashboard, you can get visibility into the health of the solution.</span></span> <span data-ttu-id="8c2a4-162">アプリケーション ログも収集されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-162">It also collected application logs.</span></span>

<span data-ttu-id="8c2a4-163">**Azure Pipelines**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-163">**Azure Pipelines**.</span></span> <span data-ttu-id="8c2a4-164">[Pipelines][pipelines] は、アプリケーションをビルド、テスト、デプロイする継続的インテグレーション (CI) および継続的デリバリー (CD) サービスです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-164">[Pipelines][pipelines] is a continuous integration (CI) and continuous delivery (CD) service that builds, tests, and deploys the application.</span></span>

## <a name="recommendations"></a><span data-ttu-id="8c2a4-165">Recommendations</span><span class="sxs-lookup"><span data-stu-id="8c2a4-165">Recommendations</span></span>

### <a name="function-app-plans"></a><span data-ttu-id="8c2a4-166">Function App プラン</span><span class="sxs-lookup"><span data-stu-id="8c2a4-166">Function App plans</span></span>

<span data-ttu-id="8c2a4-167">Azure Functions では 2 つのホスティング モデルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-167">Azure Functions supports two hosting models.</span></span> <span data-ttu-id="8c2a4-168">**従量課金プラン**では、コードの実行時にコンピューティング能力が自動的に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-168">With the **consumption plan**, compute power is automatically allocated when your code is running.</span></span>  <span data-ttu-id="8c2a4-169">**App Service** プランでは、一連の VM がお使いのコードに対して割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-169">With the **App Service** plan, a set of VMs are allocated for your code.</span></span> <span data-ttu-id="8c2a4-170">App Service プランで定義されるのは VM の数とサイズです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-170">The App Service plan defines the number of VMs and the VM size.</span></span> 

<span data-ttu-id="8c2a4-171">上記の定義に従うと、App Service プランは厳密には "*サーバーレス*" ではありません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-171">Note that the App Service plan is not strictly *serverless*, according to the definition given above.</span></span> <span data-ttu-id="8c2a4-172">プログラミング モデルは同じですが、従量課金プランと App Service プランの両方で、同じ関数コードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-172">The programming model is the same, however &mdash; the same function code can run in both a consumption plan and an App Service plan.</span></span>

<span data-ttu-id="8c2a4-173">使用するプランの種類を選択するときに検討すべき要素を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-173">Here are some factors to consider when choosing which type of plan to use:</span></span>

- <span data-ttu-id="8c2a4-174">**コールド スタート**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-174">**Cold start**.</span></span> <span data-ttu-id="8c2a4-175">従量課金プランでは、最近呼び出されていない関数の場合、次回の実行時に待ち時間が少し長くなります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-175">With the consumption plan, a function that hasn't been invoked recently will incur some additional latency the next time it runs.</span></span> <span data-ttu-id="8c2a4-176">この追加の待ち時間は、ランタイム環境の割り当てと準備が原因で発生します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-176">This additional latency is due to allocating and preparing the runtime environment.</span></span> <span data-ttu-id="8c2a4-177">通常は数秒程度ですが、読み込む必要がある依存関係の数など、いくつかの要因によって異なってきます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-177">It is usually on the order of seconds but depends on several factors, including the number of dependencies that need to be loaded.</span></span> <span data-ttu-id="8c2a4-178">詳細については、「[Understanding Serverless Cold Start (サーバーレスのコールド スタートについて)][functions-cold-start]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-178">For more information, see [Understanding Serverless Cold Start][functions-cold-start].</span></span> <span data-ttu-id="8c2a4-179">通常、コールド スタートが問題になるのは、非同期のメッセージ ドリブン ワークロード (キューまたはイベント ハブ トリガー) よりも対話型ワークロード (HTTP トリガー) です。対話型ワークロードでは、ユーザーが待ち時間が長くなったことに直接気付くためです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-179">Cold start is usually more of a concern for interactive workloads (HTTP triggers) than asynchronous message-driven workloads (queue or event hubs triggers), because the additional latency is directly observed by users.</span></span>
- <span data-ttu-id="8c2a4-180">**タイムアウト時間**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-180">**Timeout period**.</span></span>  <span data-ttu-id="8c2a4-181">従量課金プランでは、[構成可能な][functions-timeout]期間 (最大 10 分) が経過すると関数の実行はタイムアウトします</span><span class="sxs-lookup"><span data-stu-id="8c2a4-181">In the consumption plan, a function execution times out after a [configurable][functions-timeout] period of time (to a maximum of 10 minutes)</span></span>
- <span data-ttu-id="8c2a4-182">**仮想ネットワークの分離**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-182">**Virtual network isolation**.</span></span> <span data-ttu-id="8c2a4-183">App Service プランを使用すると、分離された専用ホスティング環境である [App Service 環境][ase]内で関数を実行できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-183">Using an App Service plan allows functions to run inside of an [App Service Environment][ase], which is a dedicated and isolated hosting environment.</span></span>
- <span data-ttu-id="8c2a4-184">**価格モデル**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-184">**Pricing model**.</span></span> <span data-ttu-id="8c2a4-185">従量課金プランでは、実行数とリソース消費量 (メモリ &times; 実行時間) によって課金されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-185">The consumption plan is billed by the number of executions and resource consumption (memory &times; execution time).</span></span> <span data-ttu-id="8c2a4-186">App Service プランでは、VM インスタンス SKU に基づいて 1 時間ごとに課金されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-186">The App Service plan is billed hourly based on VM instance SKU.</span></span> <span data-ttu-id="8c2a4-187">従量課金プランは、使用したコンピューティング リソースにしか支払いが発生しないため、多くの場合 App Service プランよりもコストがかかりません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-187">Often, the consumption plan can be cheaper than an App Service plan, because you pay only for the compute resources that you use.</span></span> <span data-ttu-id="8c2a4-188">これは、トラフィックに山と谷がある場合に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-188">This is especially true if your traffic experiences peaks and troughs.</span></span> <span data-ttu-id="8c2a4-189">ただし、アプリケーションのスループットが一定して高い場合は、App Service プランの方が従量課金プランよりも低コストになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-189">However, if an application experiences constant high-volume throughput, an App Service plan may cost less than the consumption plan.</span></span>
- <span data-ttu-id="8c2a4-190">**スケーリング**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-190">**Scaling**.</span></span> <span data-ttu-id="8c2a4-191">従量課金モデルの大きな利点は、着信トラフィックに基づいて、必要に応じて動的にスケーリングされることです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-191">A big advantage of the consumption model is that it scales dynamically as needed, based on the incoming traffic.</span></span> <span data-ttu-id="8c2a4-192">このスケーリングは迅速に行われますが、やはり準備期間はあります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-192">While this scaling occurs quickly, there is still a ramp-up period.</span></span> <span data-ttu-id="8c2a4-193">一部のワークロードでは、準備時間なしでトラフィックのバーストを処理できるように、VM の意図的オーバープロビジョニングが必要なことがあります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-193">For some workloads, you might want to deliberately overprovision the VMs, so that you can handle bursts of traffic with zero ramp-up time.</span></span> <span data-ttu-id="8c2a4-194">その場合は、App Service プランを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-194">In that case, consider an App Service plan.</span></span>

### <a name="function-app-boundaries"></a><span data-ttu-id="8c2a4-195">Function App の境界</span><span class="sxs-lookup"><span data-stu-id="8c2a4-195">Function App boundaries</span></span>

<span data-ttu-id="8c2a4-196">"*関数アプリ*" によって、1 つ以上の "*関数*" の実行がホストされます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-196">A *function app* hosts the execution of one or more *functions*.</span></span> <span data-ttu-id="8c2a4-197">関数アプリを使用すると、複数の関数を 1 つの論理ユニットとしてまとめることができます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-197">You can use a function app to group several functions together as a logical unit.</span></span> <span data-ttu-id="8c2a4-198">関数アプリ内の関数は、同じアプリケーション設定、ホスティング プラン、およびデプロイ ライフサイクルを共有しています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-198">Within a function app, the functions share the same application settings, hosting plan, and deployment lifecycle.</span></span> <span data-ttu-id="8c2a4-199">関数アプリはそれぞれ独自のホスト名を持ちます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-199">Each function app has its own hostname.</span></span>  

<span data-ttu-id="8c2a4-200">関数アプリを使用して、同じライフサイクルと設定を共有する関数をグループ化します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-200">Use function apps to group functions that share the same lifecycle and settings.</span></span> <span data-ttu-id="8c2a4-201">同じライフサイクルを共有していない関数は、別の関数アプリでホストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-201">Functions that don't share the same lifecycle should be hosted in different function apps.</span></span> 

<span data-ttu-id="8c2a4-202">マイクロサービス アプローチを使用することを検討します。このアプローチでは、各関数アプリが 1 つのマイクロサービスを表しており、関連する複数の関数で構成されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-202">Consider taking a microservices approach, where each function app represents one microservice, possibly consisting of several related functions.</span></span> <span data-ttu-id="8c2a4-203">マイクロサービス アーキテクチャのサービスには、疎結合と、機能の高い凝集度が必要です。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-203">In a microservices architecture, services should have loose coupling and high functional cohesion.</span></span> <span data-ttu-id="8c2a4-204">"*疎*" 結合とは、他のサービスを同時に更新しなくても、あるサービスを変更できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-204">*Loosely* coupled means you can change one service without requiring other services to be updated at the same time.</span></span> <span data-ttu-id="8c2a4-205">"*高凝集*" とは、明確に定義された 1 つの目的をサービスが持つことを意味します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-205">*Cohesive* means a service has a single, well-defined purpose.</span></span> <span data-ttu-id="8c2a4-206">これらのアイデアの詳細については、「[マイクロサービスの設計: ドメイン分析][microservices-domain-analysis]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-206">For more discussion of these ideas, see [Designing microservices: Domain analysis][microservices-domain-analysis].</span></span>

### <a name="function-bindings"></a><span data-ttu-id="8c2a4-207">関数のバインディング</span><span class="sxs-lookup"><span data-stu-id="8c2a4-207">Function bindings</span></span>

<span data-ttu-id="8c2a4-208">可能な場合は、関数の[バインディング][functions-bindings]を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-208">Use Functions [bindings][functions-bindings] when possible.</span></span> <span data-ttu-id="8c2a4-209">バインディングにより、お使いのコードをデータに接続し、他の Azure サービスと統合するために宣言型の方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-209">Bindings provide a declarative way to connect your code to data and integrate with other Azure services.</span></span> <span data-ttu-id="8c2a4-210">入力バインディングでは、入力パラメーターが外部データ ソースから設定されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-210">An input binding populates an input parameter from an external data source.</span></span> <span data-ttu-id="8c2a4-211">出力バインディングでは、キューやデータベースなどのデータ シンクに関数の戻り値が送信されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-211">An output binding sends the function's return value to a data sink, such as a queue or database.</span></span>

<span data-ttu-id="8c2a4-212">たとえば、リファレンス実装の `GetStatus` 関数では、Cosmos DB の[入力バインディング][cosmosdb-input-binding]が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-212">For example, the `GetStatus` function in the reference implementation uses the Cosmos DB [input binding][cosmosdb-input-binding].</span></span> <span data-ttu-id="8c2a4-213">このバインディングは、HTTP 要求のクエリ文字列から取得されるクエリ パラメーターを使用して、Cosmos DB のドキュメントを検索するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-213">This binding is configured to look up a document in Cosmos DB, using query parameters that are taken from the query string in the HTTP request.</span></span> <span data-ttu-id="8c2a4-214">ドキュメントが見つかった場合、そのドキュメントは関数にパラメーターとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-214">If the document is found, it is passed to the function as a parameter.</span></span>

```csharp
[FunctionName("GetStatusFunction")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req, 
    [CosmosDB(
        databaseName: "%COSMOSDB_DATABASE_NAME%",
        collectionName: "%COSMOSDB_DATABASE_COL%",
        ConnectionStringSetting = "COSMOSDB_CONNECTION_STRING",
        Id = "{Query.deviceId}",
        PartitionKey = "{Query.deviceId}")] dynamic deviceStatus, 
    ILogger log)
{
    ...
}
```

<span data-ttu-id="8c2a4-215">バインディングを使用すると、サービスと直接通信するコードを記述する必要がありません。これにより関数コードが簡単になるだけでなく、データ ソースまたはシンクの詳細が抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-215">By using bindings, you don't need to write code that talks directly to the service, which makes the function code simpler and also abstracts the details of the data source or sink.</span></span> <span data-ttu-id="8c2a4-216">ただし、場合によっては、バインディングが提供するよりも複雑なロジックが必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-216">In some cases, however, you may need more complex logic than the binding provides.</span></span> <span data-ttu-id="8c2a4-217">その場合は、Azure のクライアント SDK を直接使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-217">In that case, use the Azure client SDKs directly.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="8c2a4-218">スケーラビリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8c2a4-218">Scalability considerations</span></span>

<span data-ttu-id="8c2a4-219">**Functions**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-219">**Functions**.</span></span> <span data-ttu-id="8c2a4-220">従量課金プランの場合、HTTP トリガーはトラフィックに基づいてスケーリングされます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-220">For the consumption plan, the HTTP trigger scales based on the traffic.</span></span> <span data-ttu-id="8c2a4-221">コンカレント関数インスタンスの数には制限がありますが、それぞれのインスタンスが一度に複数の要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-221">There is a limit to the number of concurrent function instances, but each instance can process more than one request at a time.</span></span> <span data-ttu-id="8c2a4-222">App Service プランでは、HTTP トリガーは VM インスタンスの数に応じてスケーリングされます。この数は固定値にすることも、一連の自動スケーリング ルールに基づいて自動スケーリングすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-222">For an App Service plan, the HTTP trigger scales according to the number of VM instances, which can be a fixed value or can autoscale based on a set of autoscaling rules.</span></span> <span data-ttu-id="8c2a4-223">詳細については、「[Azure Functions のスケールとホスティング][functions-scale]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-223">For information, see [Azure Functions scale and hosting][functions-scale].</span></span> 

<span data-ttu-id="8c2a4-224">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-224">**Cosmos DB**.</span></span> <span data-ttu-id="8c2a4-225">Cosmos DB のスループット容量は、[要求ユニット][ru] (RU) で測定されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-225">Throughput capacity for Cosmos DB is measured in [Request Units][ru] (RU).</span></span> <span data-ttu-id="8c2a4-226">1 RU のスループットは、1 KB のドキュメントを取得するのに必要なスループットに対応します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-226">A 1-RU throughput corresponds to the throughput need to GET a 1KB document.</span></span> <span data-ttu-id="8c2a4-227">Cosmos DB コンテナーを 10,000 RU を超えてスケーリングするには、コンテナーの作成時に[パーティション キー][partition-key]を指定し、作成するすべてのドキュメントにパーティション キーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-227">In order to scale a Cosmos DB container past 10,000 RU, you must specify a [partition key][partition-key] when you create the container and include the partition key in every document that you create.</span></span> <span data-ttu-id="8c2a4-228">パーティション キーの詳細については、「[Azure Cosmos DB でのパーティション分割とスケーリング][cosmosdb-scale]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-228">For more information about partition keys, see [Partition and scale in Azure Cosmos DB][cosmosdb-scale].</span></span>

<span data-ttu-id="8c2a4-229">**API Management**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-229">**API Management**.</span></span> <span data-ttu-id="8c2a4-230">API Management はスケールアウトが可能で、ルールベースの自動スケーリングがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-230">API Management can scale out and supports rule-based autoscaling.</span></span> <span data-ttu-id="8c2a4-231">このスケーリング プロセスには、少なくとも 20 分かかることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-231">Note that the scaling process takes at least 20 minutes.</span></span> <span data-ttu-id="8c2a4-232">トラフィックのバーストが発生する場合は、最大バースト トラフィックの予測に基づいてプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-232">If your traffic is bursty, you should provision for the maximum burst traffic that you expect.</span></span> <span data-ttu-id="8c2a4-233">ただし、自動スケーリングを使えば、時間単位または日単位でトラフィックの変動が処理されるため便利です。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-233">However, autoscaling is useful for handling hourly or daily variations in traffic.</span></span> <span data-ttu-id="8c2a4-234">詳細については、「[Azure API Management インスタンスを自動的にスケーリングする][apim-scale]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-234">For more information, see [Automatically scale an Azure API Management instance][apim-scale].</span></span>

## <a name="disaster-recovery-considerations"></a><span data-ttu-id="8c2a4-235">ディザスター リカバリーの考慮事項</span><span class="sxs-lookup"><span data-stu-id="8c2a4-235">Disaster recovery considerations</span></span>

<span data-ttu-id="8c2a4-236">ここに示したデプロイは単一の Azure リージョンに存在します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-236">The deployment shown here resides in a single Azure region.</span></span> <span data-ttu-id="8c2a4-237">ディザスター リカバリーでより回復性に優れたアプローチを実現するには、さまざまなサービスで地理的分散機能を利用します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-237">For a more resilient approach to disaster-recovery, take advantage of the geo-distribution features in the various services:</span></span>

- <span data-ttu-id="8c2a4-238">API Management では複数リージョンのデプロイがサポートされており、API Management サービスの単一インスタンスを任意の数の Azure リージョンに分散できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-238">API Management supports multi-region deployment, which can be used to distribute a single API Management instance across any number of Azure regions.</span></span> <span data-ttu-id="8c2a4-239">詳細については、「[複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法][api-geo]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-239">For more information, see [How to deploy an Azure API Management service instance to multiple Azure regions][api-geo].</span></span>

- <span data-ttu-id="8c2a4-240">[Traffic Manager][tm] を使用して、HTTP 要求をプライマリ リージョンにルーティングします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-240">Use [Traffic Manager][tm] to route HTTP requests to the primary region.</span></span> <span data-ttu-id="8c2a4-241">そのリージョンで実行されている Function App が使用できなくなった場合、Traffic Manager によってセカンダリ リージョンにフェールオーバーできます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-241">If the Function App running in that region becomes unavailable, Traffic Manager can fail over to a secondary region.</span></span>

- <span data-ttu-id="8c2a4-242">Cosmos DB では[複数のマスター リージョン][cosmosdb-geo]がサポートされています。これにより、Cosmos DB アカウントに追加した任意のリージョンに書き込むことができます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-242">Cosmos DB supports [multiple master regions][cosmosdb-geo], which enables writes to any region that you add to your Cosmos DB account.</span></span> <span data-ttu-id="8c2a4-243">マルチマスターを有効にしなくても、プライマリ書き込みリージョンにはフェールオーバーできます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-243">If you don't enable multi-master, you can still fail over the primary write region.</span></span> <span data-ttu-id="8c2a4-244">フェールオーバーは、Cosmos DB クライアント SDK と Azure Functions のバインディングによって自動的に処理されるため、アプリケーション構成設定を更新する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-244">The Cosmos DB client SDKs and the Azure Function bindings automatically handle the failover, so you don't need to update any application configuration settings.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="8c2a4-245">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="8c2a4-245">Security considerations</span></span>

### <a name="authentication"></a><span data-ttu-id="8c2a4-246">Authentication</span><span class="sxs-lookup"><span data-stu-id="8c2a4-246">Authentication</span></span>

<span data-ttu-id="8c2a4-247">リファレンス実装の `GetStatus` API では、Azure AD を使用して要求が認証されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-247">The `GetStatus` API in the reference implementation uses Azure AD to authenticate requests.</span></span> <span data-ttu-id="8c2a4-248">Azure AD がサポートするのは Open ID Connect プロトコルで、この認証プロトコルは、OAuth 2 プロトコルを基盤として構築されています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-248">Azure AD supports the Open ID Connect protocol, which is an authentication protocol built on top of the OAuth 2 protocol.</span></span>

<span data-ttu-id="8c2a4-249">このアーキテクチャでは、クライアント アプリケーションは、ブラウザーで実行されるシングル ページ アプリケーション (SPA) です。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-249">In this architecture, the client application is a single-page application (SPA) that runs in the browser.</span></span> <span data-ttu-id="8c2a4-250">この種類のクライアント アプリケーションでは、クライアントをシークレットにできません。また、認証コードも非表示になりません。このため暗黙的な許可フローが適しています </span><span class="sxs-lookup"><span data-stu-id="8c2a4-250">This type of client application cannot keep a client secret or an authorization code hidden, so the implicit grant flow is appropriate.</span></span> <span data-ttu-id="8c2a4-251">([使用するべき OAuth 2.0 フロー][oauth-flow]に関するページをご覧ください)。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-251">(See [Which OAuth 2.0 flow should I use?][oauth-flow]).</span></span> <span data-ttu-id="8c2a4-252">全体的なフローを次に示します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-252">Here's the overall flow:</span></span>

1. <span data-ttu-id="8c2a4-253">ユーザーが Web アプリケーションで "サインイン" リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-253">The user clicks the "Sign in" link in the web application.</span></span>
1. <span data-ttu-id="8c2a4-254">ブラウザーが Azure AD のサインイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-254">The browser is redirected the Azure AD sign in page.</span></span> 
1. <span data-ttu-id="8c2a4-255">ユーザーがサインインします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-255">The user signs in.</span></span>
1. <span data-ttu-id="8c2a4-256">Azure AD がクライアント アプリケーションにもう一度リダイレクトされます。その URL フラグメントにはアクセス トークンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-256">Azure AD redirects back to the client application, including an access token in the URL fragment.</span></span>
1. <span data-ttu-id="8c2a4-257">Web アプリケーションが API を呼び出すとき、認証ヘッダーにはアクセス トークンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-257">When the web application calls the API, it includes the access token in the Authentication header.</span></span> <span data-ttu-id="8c2a4-258">アプリケーション ID は、アクセス トークンで audience ("aud") クレームとして送信されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-258">The application ID is sent as the audience ('aud') claim in the access token.</span></span> 
1. <span data-ttu-id="8c2a4-259">バックエンド API によってアクセス トークンが検証されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-259">The backend API validates the access token.</span></span>

<span data-ttu-id="8c2a4-260">認証を構成するには:</span><span class="sxs-lookup"><span data-stu-id="8c2a4-260">To configure authentication:</span></span>

- <span data-ttu-id="8c2a4-261">アプリケーションをお使いの Azure AD テナントに登録します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-261">Register an application in your Azure AD tenant.</span></span> <span data-ttu-id="8c2a4-262">これによりアプリケーション ID が生成されます。クライアントはこれをログイン URL に含めます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-262">This generates an application ID, which the client includes with the login URL.</span></span>

- <span data-ttu-id="8c2a4-263">Function App 内の Azure AD 認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-263">Enable Azure AD authentication inside the Function App.</span></span> <span data-ttu-id="8c2a4-264">詳細については、「[Azure App Service での認証および承認][app-service-auth]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-264">For more information, see [Authentication and authorization in Azure App Service][app-service-auth].</span></span>

- <span data-ttu-id="8c2a4-265">API Management にポリシーを追加し、アクセス トークンを検証することで要求を事前承認できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-265">Add a policy to API Management to pre-authorize the request by validating the access token:</span></span>

    ```xml
    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.microsoftonline.com/[Azure AD tenant ID]/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>[Application ID]</value>
            </claim>
        </required-claims>
    </validate-jwt>
    ```

<span data-ttu-id="8c2a4-266">詳細については、[GitHub の Readme][readme] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-266">For more details, see the [GitHub readme][readme].</span></span>

### <a name="authorization"></a><span data-ttu-id="8c2a4-267">Authorization</span><span class="sxs-lookup"><span data-stu-id="8c2a4-267">Authorization</span></span>

<span data-ttu-id="8c2a4-268">多くのアプリケーションでは、バックエンド API で、ユーザーが特定のアクションを実行するアクセス許可があるかどうかを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-268">In many applications, the backend API must check whether a user has permission to perform a given action.</span></span> <span data-ttu-id="8c2a4-269">[クレームベースの承認][claims]を使用することをお勧めします。この方法では、ユーザーに関する情報は ID プロバイダー (この例では Azure AD) によって伝達され、承認の判断に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-269">It's recommended to use [claims-based authorization][claims], where information about the user is conveyed by the identity provider (in this case, Azure AD) and used to make authorization decisions.</span></span> 

<span data-ttu-id="8c2a4-270">Azure AD がクライアントに返す ID トークン内で提供されるクレームもあります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-270">Some claims are provided inside the ID token that Azure AD returns to the client.</span></span> <span data-ttu-id="8c2a4-271">これらのクレームを関数アプリ内から取得するには、要求の X-MS-CLIENT-PRINCIPAL ヘッダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-271">You can get these claims from within the function app by examining the X-MS-CLIENT-PRINCIPAL header in the request.</span></span> <span data-ttu-id="8c2a4-272">他のクレームについては、[Microsoft Graph][graph] を使用して、Azure AD にクエリを実行します (サインイン時にユーザーの同意が必要です)。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-272">For other claims, use [Microsoft Graph][graph] to query Azure AD (requires user consent during sign-in).</span></span> 

<span data-ttu-id="8c2a4-273">たとえば、Azure AD でアプリケーションを登録するときに、アプリケーションの登録マニフェストで一連のアプリケーション ロールを定義できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-273">For example, when you register an application in Azure AD, you can define a set of application roles in the application's registration manifest.</span></span> <span data-ttu-id="8c2a4-274">ユーザーがアプリケーションにサインインするとき、Azure AD には、そのユーザーに付与されたロール (グループ メンバーシップを通じて付与されているロールを含む) ごとに "roles" クレームが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-274">When a user signs into the application, Azure AD includes a "roles" claim for each role that the user has been granted (including roles that are inherited through group membership).</span></span> 

<span data-ttu-id="8c2a4-275">リファレンス実装では、認証されたユーザーが `GetStatus` アプリケーション ロールのメンバーであるかどうかが関数によって確認されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-275">In the reference implementation, the function checks whether the authenticated user is a member of the `GetStatus` application role.</span></span> <span data-ttu-id="8c2a4-276">メンバーでない場合、関数は HTTP 未承認 (401) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-276">If not, the function returns an HTTP Unauthorized (401) response.</span></span> 

```csharp
[FunctionName("GetStatusFunction")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [CosmosDB(
        databaseName: "%COSMOSDB_DATABASE_NAME%",
        collectionName: "%COSMOSDB_DATABASE_COL%",
        ConnectionStringSetting = "COSMOSDB_CONNECTION_STRING",
        Id = "{Query.deviceId}",
        PartitionKey = "{Query.deviceId}")] dynamic deviceStatus, 
    ILogger log)
{
    log.LogInformation("Processing GetStatus request.");

    return req.HandleIfAuthorizedForRoles(new[] { GetDeviceStatusRoleName },
        async () =>
        {
            string deviceId = req.Query["deviceId"];
            if (deviceId == null)
            {
                return new BadRequestObjectResult("Missing DeviceId");
            }

            return await Task.FromResult<IActionResult>(deviceStatus != null
                    ? (ActionResult)new OkObjectResult(deviceStatus)
                    : new NotFoundResult());
        },
        log);
}
```

<span data-ttu-id="8c2a4-277">このコード例では、`HandleIfAuthorizedForRoles` はロール クレームをチェックし、クレームが見つからない場合は HTTP 401 を返す拡張メソッドです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-277">In this code example, `HandleIfAuthorizedForRoles` is an extension method that checks for the role claim and returns HTTP 401 if the claim isn't found.</span></span> <span data-ttu-id="8c2a4-278">ソース コードについては、[こちら][HttpRequestAuthorizationExtensions]を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-278">You can find the source code [here][HttpRequestAuthorizationExtensions].</span></span> <span data-ttu-id="8c2a4-279">`HandleIfAuthorizedForRoles` で `ILogger` パラメーターが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-279">Notice that `HandleIfAuthorizedForRoles` takes an `ILogger` parameter.</span></span> <span data-ttu-id="8c2a4-280">監査証跡を確保し、必要に応じて問題を診断できるように、未承認の要求をログに記録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-280">You should log unauthorized requests so that you have an audit trail and can diagnose issues if needed.</span></span> <span data-ttu-id="8c2a4-281">同時に、HTTP 401 応答の詳細情報が漏えいしないようにします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-281">At the same time, avoid leaking any detailed information inside the HTTP 401 response.</span></span>

### <a name="cors"></a><span data-ttu-id="8c2a4-282">CORS</span><span class="sxs-lookup"><span data-stu-id="8c2a4-282">CORS</span></span>

<span data-ttu-id="8c2a4-283">この参照アーキテクチャでは、Web アプリケーションと API はオリジンを共有しません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-283">In this reference architecture, the web application and the API do not share the same origin.</span></span> <span data-ttu-id="8c2a4-284">つまり、アプリケーションが API を呼び出す場合、それはクロス オリジン要求になります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-284">That means when the application calls the API, it is a cross-origin request.</span></span> <span data-ttu-id="8c2a4-285">ブラウザーのセキュリティ機能により、Web ページでは AJAX 要求を別のドメインに送信することはできません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-285">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="8c2a4-286">この制限は、"*同一オリジン ポリシー*" と呼ばれ、悪意のあるサイトが、別のサイトから機密データを読み取れないようにします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-286">This restriction is called the *same-origin policy* and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="8c2a4-287">クロス オリジン要求を有効にするには、クロス オリジン リソース共有 (CORS) [ポリシー][cors-policy]を API Management ゲートウェイに追加します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-287">To enable a cross-origin request, add a Cross-Origin Resource Sharing (CORS) [policy][cors-policy] to the API Management gateway:</span></span>

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <origin>[Website URL]</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
</cors>
```

<span data-ttu-id="8c2a4-288">この例では、**allow-credentials** 属性が **true** になっています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-288">In this example, the **allow-credentials** attribute is **true**.</span></span> <span data-ttu-id="8c2a4-289">これにより、ブラウザーが要求と共に資格情報 (Cookie など) を送信することが承認されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-289">This authorizes the browser to send credentials (including cookies) with the request.</span></span> <span data-ttu-id="8c2a4-290">それ以外の場合、既定では、ブラウザーからクロス オリジン要求と共に資格情報が送信されません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-290">Otherwise, by default the browser does not send credentials with a cross-origin request.</span></span>

> [!NOTE] 
> <span data-ttu-id="8c2a4-291">**allow-credentials** を **true** に設定すると、Web サイトが、ユーザーの資格情報を、そのユーザーが知らないところでユーザーの代わりに API に送信できるようになるため、注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-291">Be very careful about setting **allow-credentials** to **true**, because it means a website can send the user's credentials to your API on the user's behalf, without the user being aware.</span></span> <span data-ttu-id="8c2a4-292">許可されたオリジンを信頼する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-292">You must trust the allowed origin.</span></span>

### <a name="enforce-https"></a><span data-ttu-id="8c2a4-293">HTTPS の適用</span><span class="sxs-lookup"><span data-stu-id="8c2a4-293">Enforce HTTPS</span></span>

<span data-ttu-id="8c2a4-294">セキュリティを最大限に高めるには、要求パイプライン全体で HTTPS を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-294">For maximum security, require HTTPS throughout the request pipeline:</span></span>

- <span data-ttu-id="8c2a4-295">**CDN**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-295">**CDN**.</span></span> <span data-ttu-id="8c2a4-296">既定では、Azure CDN は、`*.azureedge.net` サブドメインで HTTPS をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-296">Azure CDN supports HTTPS on the `*.azureedge.net` subdomain by default.</span></span> <span data-ttu-id="8c2a4-297">CDN でカスタム ドメイン名に対して HTTPS を有効にするには、「[チュートリアル: Azure CDN カスタム ドメインで HTTPS を構成する][cdn-https]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-297">To enable HTTPS in the CDN for custom domain names, see [Tutorial: Configure HTTPS on an Azure CDN custom domain][cdn-https].</span></span> 

- <span data-ttu-id="8c2a4-298">**静的 Web サイトのホスティング**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-298">**Static website hosting**.</span></span> <span data-ttu-id="8c2a4-299">ストレージ アカウントで [[安全な転送が必須]][storage-https] オプションを有効にします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-299">Enable the "[Secure transfer required][storage-https]" option on the Storage account.</span></span> <span data-ttu-id="8c2a4-300">このオプションを有効にすると、ストレージ アカウントでは、セキュリティで保護された HTTPS 接続からの要求のみが許可されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-300">When this option is enabled, the storage account only allows requests from secure HTTPS connections.</span></span> 

- <span data-ttu-id="8c2a4-301">**API Management**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-301">**API Management**.</span></span> <span data-ttu-id="8c2a4-302">HTTPS プロトコルのみを使用するように API を構成します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-302">Configure the APIs to use HTTPS protocol only.</span></span> <span data-ttu-id="8c2a4-303">これは、Azure portal または Resource Manager テンプレートを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-303">You can configure this in the Azure portal or through a Resource Manager template:</span></span>

    ```json
    {
        "apiVersion": "2018-01-01",
        "type": "apis",
        "name": "dronedeliveryapi",
        "dependsOn": [
            "[concat('Microsoft.ApiManagement/service/', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "displayName": "Drone Delivery API",
            "description": "Drone Delivery API",
            "path": "api",
            "protocols": [ "HTTPS" ]
        },
        ...
    }
    ```

- <span data-ttu-id="8c2a4-304">**Azure Functions**。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-304">**Azure Functions**.</span></span> <span data-ttu-id="8c2a4-305">[[HTTPS のみ]][functions-https] の設定を有効にします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-305">Enable the "[HTTPS Only][functions-https]" setting.</span></span> 

### <a name="lock-down-the-function-app"></a><span data-ttu-id="8c2a4-306">関数アプリのロックダウン</span><span class="sxs-lookup"><span data-stu-id="8c2a4-306">Lock down the function app</span></span>

<span data-ttu-id="8c2a4-307">関数へのすべての呼び出しが、API ゲートウェイを経由する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-307">All calls to the function should go through the API gateway.</span></span> <span data-ttu-id="8c2a4-308">これは次のように実現できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-308">You can achieve this as follows:</span></span>

- <span data-ttu-id="8c2a4-309">関数キーを必要とするように関数アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-309">Configure the function app to require a function key.</span></span> <span data-ttu-id="8c2a4-310">API Management ゲートウェイが関数アプリを呼び出すと、そのゲートウェイに関数キーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-310">The API Management gateway will include the function key when it calls the function app.</span></span> <span data-ttu-id="8c2a4-311">これにより、クライアントはゲートウェイをバイパスして直接関数を呼び出すことができなくなります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-311">This prevents clients from calling the function directly, bypassing the gateway.</span></span> 

- <span data-ttu-id="8c2a4-312">API Management ゲートウェイには[静的 IP アドレス][apim-ip]があります。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-312">The API Management gateway has a [static IP address][apim-ip].</span></span> <span data-ttu-id="8c2a4-313">その静的 IP アドレスからの呼び出しのみを許可するように Azure 関数を制限します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-313">Restrict the Azure Function to allow only calls from that static IP address.</span></span> <span data-ttu-id="8c2a4-314">詳細については、「[Azure App Service 静的 IP 制限][app-service-ip-restrictions]」を参照してください </span><span class="sxs-lookup"><span data-stu-id="8c2a4-314">For more information, see [Azure App Service Static IP Restrictions][app-service-ip-restrictions].</span></span> <span data-ttu-id="8c2a4-315">(この機能は、Standard レベルのサービスでのみ使用できます)。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-315">(This feature is available for Standard tier services only.)</span></span> 

### <a name="protect-application-secrets"></a><span data-ttu-id="8c2a4-316">アプリケーション シークレットの保護</span><span class="sxs-lookup"><span data-stu-id="8c2a4-316">Protect application secrets</span></span>

<span data-ttu-id="8c2a4-317">データベースの資格情報などのアプリケーション シークレット情報を、コードや構成ファイルに保存しないでください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-317">Don't store application secrets, such as database credentials, in your code or configuration files.</span></span> <span data-ttu-id="8c2a4-318">代わりに、暗号化された状態で Azure に保存されているアプリ設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-318">Instead, use App settings, which are stored encrypted in Azure.</span></span> <span data-ttu-id="8c2a4-319">詳細については、「[Azure App Service と Azure Functions のセキュリティ][app-service-security]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-319">For more information, see [Security in Azure App Service and Azure Functions][app-service-security].</span></span>

<span data-ttu-id="8c2a4-320">また、アプリケーション シークレット情報を Key Vault に格納することもできます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-320">Alternatively, you can store application secrets in Key Vault.</span></span> <span data-ttu-id="8c2a4-321">これにより、シークレットのストレージを一元化し、その配布を制御して、シークレットへのアクセス方法とそのタイミングを監視することができます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-321">This allows you to centralize the storage of secrets, control their distribution, and monitor how and when secrets are being accessed.</span></span> <span data-ttu-id="8c2a4-322">詳細については、[キー コンテナーからシークレットを読み取るように Azure Web アプリケーションを構成する][key-vault-web-app]方法に関するチュートリアルをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-322">For more information, see [Configure an Azure web application to read a secret from Key Vault][key-vault-web-app].</span></span> <span data-ttu-id="8c2a4-323">ただし、その構成設定は、関数のトリガーとバインディングにより、アプリ設定から読み込まれることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-323">However, note that Functions triggers and bindings load their configuration settings from app settings.</span></span> <span data-ttu-id="8c2a4-324">Key Vault のシークレットを使用するようにトリガーとバインディングを構成する方法は、組み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-324">There is no built-in way to configure the triggers and bindings to use Key Vault secrets.</span></span>

## <a name="devops-considerations"></a><span data-ttu-id="8c2a4-325">DevOps の考慮事項</span><span class="sxs-lookup"><span data-stu-id="8c2a4-325">DevOps considerations</span></span>

### <a name="api-versioning"></a><span data-ttu-id="8c2a4-326">API のバージョン管理</span><span class="sxs-lookup"><span data-stu-id="8c2a4-326">API versioning</span></span>

<span data-ttu-id="8c2a4-327">API は、サービスとそのサービスのクライアントまたはコンシューマー間のコントラクトです。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-327">An API is a contract between a service and clients or consumers of that service.</span></span> <span data-ttu-id="8c2a4-328">API コントラクトのバージョン管理をサポートします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-328">Support versioning in your API contract.</span></span> <span data-ttu-id="8c2a4-329">API の重大な変更を導入する場合は、新しい API バージョンに変更してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-329">If you introduce a breaking API change, introduce a new API version.</span></span> <span data-ttu-id="8c2a4-330">新しいバージョンは、別の関数アプリで、元のバージョンと共にデプロイしてください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-330">Deploy the new version side-by-side with the original version, in a separate Function App.</span></span> <span data-ttu-id="8c2a4-331">これにより、クライアント アプリケーションを中断することなく、既存のクライアントを新しい API に移行できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-331">This lets you migrate existing clients to the new API without breaking client applications.</span></span> <span data-ttu-id="8c2a4-332">最終的には、以前のバージョンを廃止できます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-332">Eventually, you can deprecate the previous version.</span></span> <span data-ttu-id="8c2a4-333">API のバージョン管理の詳細については、「[RESTful Web API のバージョン管理][api-versioning]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-333">For more information about API versioning, see [Versioning a RESTful web API][api-versioning].</span></span>

<span data-ttu-id="8c2a4-334">API の破壊的変更とはならない更新プログラムの場合、新しいバージョンは、同じ関数アプリのステージング スロットにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-334">For updates that are not breaking API changes, deploy the new version to a staging slot in the same Function App.</span></span> <span data-ttu-id="8c2a4-335">デプロイが成功したことを確認したら、ステージング バージョンを運用バージョンと入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-335">Verify the deployment succeeded and then swap the staged version with the production version.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="8c2a4-336">ソリューションのデプロイ方法</span><span class="sxs-lookup"><span data-stu-id="8c2a4-336">Deploy the solution</span></span>

<span data-ttu-id="8c2a4-337">この参照アーキテクチャをデプロイするには、[GitHub の Readme][readme] をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c2a4-337">To deploy this reference architecture, view the [GitHub readme][readme].</span></span> 

<!-- links -->

[api-versioning]: ../../best-practices/api-design.md#versioning-a-restful-web-api
[apim]: /azure/api-management/api-management-key-concepts
[apim-ip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[api-geo]: /azure/api-management/api-management-howto-deploy-multi-region
[apim-scale]: /azure/api-management/api-management-howto-autoscale
[app-service-auth]: /azure/app-service/app-service-authentication-overview
[app-service-ip-restrictions]: /azure/app-service/app-service-ip-restrictions
[app-service-security]: /azure/app-service/app-service-security
[ase]: /azure/app-service/environment/intro
[azure-messaging]: /azure/event-grid/compare-messaging-services
[claims]: https://en.wikipedia.org/wiki/Claims-based_identity
[cdn]: https://azure.microsoft.com/services/cdn/
[cdn-https]: /azure/cdn/cdn-custom-ssl
[cors-policy]: /azure/api-management/api-management-cross-domain-policies
[cosmosdb]: /azure/cosmos-db/introduction
[cosmosdb-geo]: /azure/cosmos-db/distribute-data-globally
[cosmosdb-input-binding]: /azure/azure-functions/functions-bindings-cosmosdb-v2#input
[cosmosdb-scale]: /azure/cosmos-db/partition-data
[event-driven]: ../../guide/architecture-styles/event-driven.md
[functions]: /azure/azure-functions/functions-overview
[functions-bindings]: /azure/azure-functions/functions-triggers-bindings
[functions-cold-start]: https://blogs.msdn.microsoft.com/appserviceteam/2018/02/07/understanding-serverless-cold-start/
[functions-https]: /azure/app-service/app-service-web-tutorial-custom-ssl#enforce-https
[functions-proxy]: /azure-functions/functions-proxies
[functions-scale]: /azure/azure-functions/functions-scale
[functions-timeout]: /azure/azure-functions/functions-scale#consumption-plan
[graph]: https://developer.microsoft.com/graph/docs/concepts/overview
[key-vault-web-app]: /azure/key-vault/tutorial-web-application-keyvault
[microservices-domain-analysis]: ../../microservices/domain-analysis.md
[monitor]: /azure/azure-monitor/overview
[oauth-flow]: https://auth0.com/docs/api-auth/which-oauth-flow-to-use
[partition-key]: /azure/cosmos-db/partition-data
[pipelines]: /azure/devops/pipelines/index
[ru]: /azure/cosmos-db/request-units
[static-hosting]: /azure/storage/blobs/storage-blob-static-website
[static-hosting-preview]: https://azure.microsoft.com/blog/azure-storage-static-web-hosting-public-preview/
[storage-https]: /azure/storage/common/storage-require-secure-transfer
[tm]: /azure/traffic-manager/traffic-manager-overview

[github]: https://github.com/mspnp/serverless-reference-implementation
[HttpRequestAuthorizationExtensions]: https://github.com/mspnp/serverless-reference-implementation/blob/master/src/DroneStatus/dotnet/DroneStatusFunctionApp/HttpRequestAuthorizationExtensions.cs
[readme]: https://github.com/mspnp/serverless-reference-implementation/blob/master/README.md