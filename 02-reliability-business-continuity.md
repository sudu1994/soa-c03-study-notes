# Domain 2: Reliability and Business Continuity (22%)

> 🔰 <b>N</b>ew <b>t</b>o <b>a</b> <b>te</b>rm? <b>Conc</b>epts <b>li</b>nk <b>t</b>o <b>pla</b>in-<b>Engl</b>ish <b>explan</b>ations <b>i</b>n [<b>t</b>he <b>bas</b>ics <b>pa</b>ge](00-cloudops-basics.md) <b>o</b>n <b>fir</b>st <b>ment</b>ion, <b>a</b>nd <b>eve</b>ry <b>abbrev</b>iation <b>i</b>s <b>spel</b>led <b>o</b>ut <b>o</b>n <b>fir</b>st <b>u</b>se <b>a</b>nd <b>lis</b>ted <b>i</b>n <b>t</b>he [<b>glos</b>sary](00-abbreviations-glossary.md).

> **About 11 of the 50 scored questions.**
>
> <b>Tes</b>ts <b>whet</b>her <b>y</b>ou <b>c</b>an <b>ke</b>ep <b>workl</b>oads <b>u</b>p <b>a</b>s <b>lo</b>ad <b>chan</b>ges (<b>scal</b>ing, [<b>cach</b>ing](00-cloudops-basics.md#caching), <b>data</b>base <b>scal</b>ing), <b>surv</b>ive <b>compo</b>nent <b>a</b>nd <b>Availa</b>bility <b>Zo</b>ne (AZ) <b>fail</b>ures (<b>lo</b>ad <b>bala</b>ncer <b>a</b>nd <b>Rou</b>te 53 <b>hea</b>lth <b>che</b>cks, [<b>Mul</b>ti-AZ](00-cloudops-basics.md#multi-az)), <b>a</b>nd <b>reco</b>ver <b>da</b>ta <b>a</b>nd <b>who</b>le <b>enviro</b>nments (<b>back</b>ups, [<b>snaps</b>hots](00-cloudops-basics.md#backup-vs-snapshot-vs-replication), [<b>poi</b>nt-<b>i</b>n-<b>ti</b>me](00-cloudops-basics.md#point-in-time-recovery-pitr) <b>rest</b>ore, [<b>versi</b>oning](00-cloudops-basics.md#versioning), <b>Disa</b>ster <b>Reco</b>very [DR] <b>strat</b>egies).
>
> [<b>Ta</b>gs](00-cloudops-basics.md#tags) <b>li</b>ke `[also: DOP]` <b>ma</b>rk <b>mate</b>rial <b>y</b>ou <b>m</b>et <b>i</b>n <b>anot</b>her <b>ex</b>am. <b>I</b>t <b>i</b>s <b>sti</b>ll <b>cove</b>red <b>i</b>n <b>fu</b>ll <b>he</b>re.

## Table of contents

- [<b>Ex</b>am <b>tas</b>ks <b>a</b>nd <b>ski</b>lls](#exam-tasks-and-skills)
- [<b>Ta</b>sk 2.1: <b>Scalab</b>ility <b>a</b>nd <b>elast</b>icity](#task-21-scalability-and-elasticity)
- [<b>Ta</b>sk 2.2: <b>Hig</b>hly <b>avail</b>able <b>a</b>nd <b>resil</b>ient <b>enviro</b>nments](#task-22-highly-available-and-resilient-environments)
- [<b>Ta</b>sk 2.3: <b>Bac</b>kup <b>a</b>nd <b>rest</b>ore <b>strat</b>egies](#task-23-backup-and-restore-strategies)
- [<b>Gotc</b>has <b>a</b>nd <b>ex</b>am <b>tra</b>ps](#gotchas-and-exam-traps)
- [<b>Qui</b>ck-<b>rec</b>all <b>summ</b>ary](#quick-recall-summary)

## Exam tasks and skills

> <b>Summa</b>rized <b>fr</b>om <b>t</b>he <b>offi</b>cial SOA-C03 <b>ex</b>am <b>gui</b>de (v1.2). <b>Exa</b>ct <b>word</b>ing: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03-domain2.html

### Task 2.1: Implement scalability and elasticity
- **2.1.1** <b>Confi</b>gure <b>a</b>nd <b>man</b>age <b>scal</b>ing <b>mecha</b>nisms <b>i</b>n <b>comp</b>ute <b>enviro</b>nments.
- **2.1.2** <b>Imple</b>ment <b>cach</b>ing <b>t</b>o <b>enha</b>nce <b>dyna</b>mic <b>scalab</b>ility (<b>Cloud</b>Front, <b>Elasti</b>Cache).
- **2.1.3** <b>Confi</b>gure <b>a</b>nd <b>man</b>age <b>scal</b>ing <b>i</b>n <b>mana</b>ged <b>datab</b>ases (<b>Relat</b>ional <b>Data</b>base <b>Serv</b>ice [RDS], <b>Dyna</b>moDB).

### Task 2.2: Implement highly available and resilient environments
- **2.2.1** <b>Confi</b>gure <b>a</b>nd <b>troubl</b>eshoot <b>Elas</b>tic <b>Lo</b>ad <b>Balan</b>cing (ELB) <b>a</b>nd <b>Rou</b>te 53 <b>hea</b>lth <b>che</b>cks.
- **2.2.2** <b>Confi</b>gure [<b>fau</b>lt-<b>tole</b>rant](00-cloudops-basics.md#high-availability-vs-fault-tolerance) <b>syst</b>ems (<b>Mul</b>ti-AZ <b>deploy</b>ments).

### Task 2.3: Implement backup and restore strategies
- **2.3.1** <b>Auto</b>mate <b>snaps</b>hots <b>a</b>nd <b>back</b>ups (<b>Elas</b>tic <b>Comp</b>ute <b>Clo</b>ud [EC2], RDS, <b>Elas</b>tic <b>Blo</b>ck <b>Sto</b>re [EBS], <b>Sim</b>ple <b>Stor</b>age <b>Serv</b>ice [S3], <b>Dyna</b>moDB) <b>usi</b>ng <b>Ama</b>zon <b>W</b>eb <b>Serv</b>ices (AWS) <b>Bac</b>kup <b>a</b>nd <b>oth</b>er <b>serv</b>ices.
- **2.3.2** <b>Rest</b>ore <b>datab</b>ases (<b>poi</b>nt-<b>i</b>n-<b>ti</b>me <b>rest</b>ore <b>a</b>nd <b>oth</b>ers) <b>t</b>o <b>me</b>et <b>Reco</b>very <b>Ti</b>me <b>Objec</b>tive (RTO), [<b>Reco</b>very <b>Poi</b>nt <b>Objec</b>tive [RPO]](00-cloudops-basics.md#rpo-and-rto) <b>a</b>nd <b>co</b>st <b>requir</b>ements.
- **2.3.3** <b>Imple</b>ment <b>versi</b>oning <b>f</b>or <b>stor</b>age <b>serv</b>ices (S3, <b>F</b>Sx).
- **2.3.4** <b>Fol</b>low DR <b>proce</b>dures <b>a</b>nd <b>be</b>st <b>pract</b>ices (<b>bac</b>kup <b>a</b>nd <b>rest</b>ore, <b>pil</b>ot <b>lig</b>ht, [<b>wa</b>rm <b>stan</b>dby](00-cloudops-basics.md#disaster-recovery-strategies), <b>act</b>ive/<b>act</b>ive).

---

## Task 2.1: Scalability and elasticity

### Amazon EC2 Auto Scaling (skill 2.1.1) `[also: DOP, SAA]`

- **What it is & key terms:**
  - **Auto Scaling group (ASG)**: <b>a</b> <b>fle</b>et <b>wi</b>th **min / desired / max** <b>capa</b>city <b>acr</b>oss <b>subn</b>ets <b>i</b>n <b>mult</b>iple <b>Availa</b>bility <b>Zon</b>es (AZs).
  - **Launch template** (<b>versi</b>oned: <b>Ama</b>zon <b>Mach</b>ine <b>Ima</b>ge [AMI], <b>inst</b>ance <b>ty</b>pe(<b>s</b>), <b>k</b>ey <b>pa</b>ir, <b>Secu</b>rity <b>Gro</b>ups [SGs], <b>Iden</b>tity <b>a</b>nd <b>Acc</b>ess <b>Manag</b>ement [IAM] <b>inst</b>ance <b>prof</b>ile, <b>us</b>er <b>da</b>ta, EBS, <b>meta</b>data <b>opti</b>ons). **Launch configurations are deprecated** (<b>can</b>'t <b>u</b>se <b>n</b>ew <b>inst</b>ance <b>typ</b>es/<b>feat</b>ures).
  - **Mixed instances policy**: <b>mult</b>iple <b>inst</b>ance <b>typ</b>es/<b>attri</b>bute-<b>bas</b>ed <b>selec</b>tion, <b>O</b>n-<b>Dem</b>and <b>ba</b>se + <b>perce</b>ntage, **Spot allocation strategy** (`price-capacity-optimized` <b>recomm</b>ended), **capacity rebalancing** <b>f</b>or <b>Sp</b>ot.
  - **Health checks**: EC2 <b>sta</b>tus <b>che</b>cks (<b>defa</b>ult) + **ELB health checks** (<b>ena</b>ble <b>s</b>o <b>a</b>pp <b>fail</b>ures <b>repl</b>ace <b>insta</b>nces) + <b>cus</b>tom (`SetInstanceHealth`); **[health check](00-cloudops-basics.md#health-check) grace period** (<b>defa</b>ult 300 <b>s</b> <b>i</b>n <b>cons</b>ole) <b>avo</b>ids <b>repla</b>cing <b>boot</b>ing <b>insta</b>nces.
- **Scaling policies:**
  - **Target tracking** (<b>ke</b>ep `ASGAverageCPUUtilization`, `ALBRequestCountPerTarget`, <b>netw</b>ork, <b>o</b>r <b>a</b> <b>cus</b>tom [<b>met</b>ric](00-cloudops-basics.md#metric) <b>a</b>t <b>a</b> <b>tar</b>get; AWS <b>crea</b>tes <b>a</b>nd <b>mana</b>ges <b>t</b>he <b>ala</b>rms; <b>don</b>'t <b>ed</b>it <b>th</b>em).
  - **Step scaling** (<b>diffe</b>rent <b>adjust</b>ments <b>p</b>er [<b>ala</b>rm](00-cloudops-basics.md#alarm) <b>bre</b>ach <b>si</b>ze; <b>us</b>es **instance warm-up**).
  - **Simple scaling** (<b>o</b>ne <b>adjus</b>tment <b>th</b>en **cooldown**, <b>defa</b>ult 300 <b>s</b>; <b>leg</b>acy).
  - **Scheduled actions** ([<b>cr</b>on](00-cloudops-basics.md#cron-and-rate-expressions), <b>ti</b>me <b>zo</b>ne; <b>cha</b>nge <b>m</b>in/<b>m</b>ax/<b>desi</b>red).
  - **Predictive scaling** (<b>fore</b>cast <b>fr</b>om 14 <b>da</b>ys <b>o</b>f <b>hist</b>ory; **forecast only** <b>mo</b>de <b>t</b>o <b>eval</b>uate <b>fir</b>st; <b>laun</b>ches <b>capa</b>city <b>ahe</b>ad <b>o</b>f <b>cyc</b>les).
  - **Queue-based**: <b>tar</b>get <b>trac</b>king <b>o</b>n <b>a</b> **custom "backlog per instance"** <b>met</b>ric (`ApproximateNumberOfMessagesVisible` <b>÷</b> <b>InSer</b>vice <b>insta</b>nces) <b>v</b>s <b>accep</b>table <b>back</b>log.
- **Lifecycle features:** **lifecycle hooks** (`Pending:Wait` / `Terminating:Wait`, <b>defa</b>ult <b>heart</b>beat 1 <b>ho</b>ur, <b>comp</b>lete <b>wi</b>th `CompleteLifecycleAction`; <b>e</b>.g., <b>inst</b>all <b>soft</b>ware, <b>dra</b>in <b>connec</b>tions, <b>sh</b>ip <b>lo</b>gs <b>v</b>ia <b>EventB</b>ridge → <b>Lam</b>bda/SSM), **warm pools** (<b>p</b>re-<b>initia</b>lized <b>insta</b>nces <b>ke</b>pt `Stopped`, `Running` <b>o</b>r `Hibernated` <b>f</b>or <b>fa</b>st <b>sca</b>le-<b>o</b>ut), **instance refresh** ([<b>roll</b>ing](00-cloudops-basics.md#deployment-strategies) <b>replac</b>ement <b>t</b>o <b>a</b> <b>n</b>ew <b>lau</b>nch <b>temp</b>late/AMI <b>wi</b>th **minimum healthy percentage**, <b>checkp</b>oints, **skip matching**, **auto rollback** <b>o</b>n <b>Cloud</b>Watch <b>ala</b>rm), **instance maintenance policy** (<b>lau</b>nch <b>bef</b>ore <b>termi</b>nate <b>v</b>s <b>termi</b>nate <b>bef</b>ore <b>lau</b>nch), **scale-in protection**, **standby** <b>sta</b>te (<b>troubl</b>eshoot <b>with</b>out <b>termin</b>ation), **suspend processes** (<b>e</b>.g., <b>susp</b>end `ReplaceUnhealthy`/`HealthCheck` <b>whi</b>le <b>debug</b>ging, `AZRebalance`), **termination policies** (<b>defa</b>ult: AZ <b>bala</b>nce → <b>old</b>est <b>lau</b>nch <b>temp</b>late/<b>con</b>fig → <b>clos</b>est <b>t</b>o <b>ne</b>xt <b>bill</b>ing <b>ho</b>ur; <b>cus</b>tom: `OldestInstance`, `NewestInstance`, `OldestLaunchTemplate`, `AllocationStrategy`, <b>cus</b>tom <b>Lam</b>bda).
- **Troubleshooting scaling (common):**
  - <b>Insta</b>nces <b>lau</b>nch <b>th</b>en <b>termi</b>nate <b>i</b>n <b>a</b> <b>lo</b>op → <b>fail</b>ing **ELB health checks** (<b>wro</b>ng <b>pa</b>th/<b>po</b>rt, <b>Secu</b>rity <b>Gro</b>up [SG] <b>blo</b>cks LB, <b>a</b>pp <b>sl</b>ow → <b>rai</b>se <b>gra</b>ce <b>per</b>iod).
  - ASG <b>n</b>ot <b>launc</b>hing → **InsufficientInstanceCapacity** (<b>a</b>dd <b>inst</b>ance <b>typ</b>es/AZs), **virtual CPU (vCPU) service [quota](00-cloudops-basics.md#service-quotas)** <b>reac</b>hed, <b>inva</b>lid AMI/<b>k</b>ey <b>pa</b>ir/SG, <b>lau</b>nch <b>temp</b>late <b>miss</b>ing <b>permis</b>sions (<b>K</b>ey <b>Manag</b>ement <b>Serv</b>ice [KMS] <b>k</b>ey <b>f</b>or <b>encry</b>pted EBS: <b>t</b>he **service-linked role** <b>nee</b>ds <b>k</b>ey <b>acc</b>ess), <b>sub</b>net <b>o</b>ut <b>o</b>f IP <b>addre</b>sses (IPs).
  - <b>N</b>ot <b>scal</b>ing <b>o</b>ut <b>tho</b>ugh <b>Cent</b>ral <b>Proce</b>ssing <b>Un</b>it (CPU) <b>hi</b>gh → <b>m</b>ax <b>capa</b>city <b>reac</b>hed, <b>cool</b>down/<b>wa</b>rm-<b>u</b>p, <b>ala</b>rm <b>o</b>n <b>wro</b>ng <b>met</b>ric/<b>dimen</b>sion, <b>suspe</b>nded <b>proce</b>sses.
  - <b>Scal</b>ing <b>t</b>oo <b>slo</b>wly → <b>lo</b>ng <b>bo</b>ot (<b>ba</b>ke <b>Ama</b>zon <b>Mach</b>ine <b>Ima</b>ges [AMIs], <b>wa</b>rm <b>poo</b>ls), <b>predi</b>ctive <b>scal</b>ing, <b>smal</b>ler <b>st</b>ep <b>thres</b>holds, <b>deta</b>iled <b>monit</b>oring.
- **Other compute scaling:**
  - **Elastic Container Service (ECS) service auto scaling** (<b>Applic</b>ation <b>Au</b>to <b>Scal</b>ing: <b>tar</b>get <b>trac</b>king <b>o</b>n `ECSServiceAverageCPUUtilization`/<b>mem</b>ory/ALB <b>requ</b>est <b>cou</b>nt; **capacity providers** + **cluster auto scaling** <b>t</b>o <b>sca</b>le EC2 <b>insta</b>nces <b>t</b>o <b>f</b>it <b>tas</b>ks; <b>Farg</b>ate <b>nee</b>ds <b>n</b>o <b>inst</b>ance <b>scal</b>ing).
  - **Elastic Kubernetes Service (EKS)**: [<b>Horiz</b>ontal](00-cloudops-basics.md#horizontal-vs-vertical-scaling) <b>P</b>od <b>Autos</b>caler (<b>po</b>ds), **Karpenter** <b>o</b>r <b>Clus</b>ter <b>Autos</b>caler (<b>nod</b>es), **EKS Auto Mode** (AWS-<b>mana</b>ged <b>nod</b>es).
  - **Lambda**: <b>sca</b>les <b>automat</b>ically <b>p</b>er <b>requ</b>est; **reserved concurrency** (<b>c</b>ap/<b>guara</b>ntee), **provisioned concurrency** (<b>p</b>re-<b>war</b>med; <b>c</b>an <b>au</b>to <b>sca</b>le <b>wi</b>th <b>Applic</b>ation <b>Au</b>to <b>Scal</b>ing <b>o</b>n <b>sche</b>dule/<b>tar</b>get <b>trac</b>king); <b>acco</b>unt <b>concur</b>rency <b>quo</b>ta (<b>defa</b>ult 1,000; <b>requ</b>est <b>incr</b>ease).
  - **AWS Auto Scaling scaling plans** <b>t</b>o <b>man</b>age <b>mult</b>iple <b>reso</b>urce <b>typ</b>es <b>toge</b>ther.
- **Exam triggers:** "<b>ke</b>ep <b>aver</b>age CPU <b>a</b>t 50%" → **target tracking**. "<b>Traf</b>fic <b>sur</b>ge <b>eve</b>ry <b>Mon</b>day 9 AM" → **scheduled** (<b>o</b>r <b>predi</b>ctive). "<b>Insta</b>nces <b>ne</b>ed 10 <b>minu</b>tes <b>t</b>o <b>bo</b>ot <b>a</b>nd <b>sca</b>le-<b>o</b>ut <b>i</b>s <b>t</b>oo <b>sl</b>ow" → **warm pool**. "<b>R</b>un <b>a</b> <b>scr</b>ipt <b>bef</b>ore <b>a</b>n <b>inst</b>ance <b>termi</b>nates" → **lifecycle hook**. "<b>Ro</b>ll <b>o</b>ut <b>a</b> <b>n</b>ew AMI <b>t</b>o <b>t</b>he ASG <b>gradu</b>ally <b>a</b>nd <b>ro</b>ll <b>ba</b>ck <b>o</b>n <b>ala</b>rms" → **instance refresh** <b>wi</b>th <b>au</b>to <b>roll</b>back.

### Caching for dynamic scalability (skill 2.1.2) `[also: SAA]`

- **Amazon CloudFront** (<b>ed</b>ge <b>cach</b>ing): <b>ser</b>ves <b>cac</b>hed <b>respo</b>nses <b>fr</b>om <b>ed</b>ge <b>locat</b>ions, <b>absor</b>bing <b>traf</b>fic <b>s</b>o <b>t</b>he <b>ori</b>gin <b>sca</b>les <b>le</b>ss. <b>Lev</b>ers: **cache policies** (TTLs, [<b>cac</b>he <b>k</b>ey](00-cloudops-basics.md#cdn-origin-and-cache-key) = <b>pa</b>th + <b>sele</b>cted <b>head</b>ers/<b>cook</b>ies/<b>que</b>ry <b>stri</b>ngs), **origin request policies** (<b>wha</b>t's <b>forwa</b>rded <b>with</b>out <b>bei</b>ng <b>i</b>n <b>t</b>he <b>k</b>ey), `Cache-Control`/`Expires` <b>head</b>ers <b>fr</b>om <b>t</b>he <b>ori</b>gin, **Origin Shield** (<b>ext</b>ra <b>regi</b>onal <b>cach</b>ing <b>lay</b>er <b>th</b>at <b>colla</b>pses <b>requ</b>ests), <b>compre</b>ssion, <b>cach</b>ing **dynamic content** <b>wi</b>th <b>sho</b>rt TTLs, **stale-while-revalidate**/`stale-if-error`. <b>Moni</b>tor **`CacheHitRate`** (<b>ena</b>ble <b>addit</b>ional <b>metr</b>ics), `OriginLatency`, `4xx/5xxErrorRate`. <b>Deta</b>ils <b>a</b>nd <b>troubles</b>hooting <b>i</b>n <b>Dom</b>ain 5.
- **Amazon ElastiCache** (<b>i</b>n-<b>mem</b>ory <b>cac</b>he):
  - **Engines**: **Valkey** <b>a</b>nd **Redis OSS** (<b>replic</b>ation, <b>Mul</b>ti-AZ <b>autom</b>atic <b>fail</b>over, <b>clus</b>ter <b>mo</b>de <b>shar</b>ding, <b>persis</b>tence, <b>ri</b>ch <b>da</b>ta <b>typ</b>es, <b>p</b>ub/<b>s</b>ub) <b>a</b>nd **Memcached** (<b>mul</b>ti-<b>thre</b>aded, <b>sim</b>ple, <b>n</b>o <b>replic</b>ation/<b>persis</b>tence).
  - **Deployment**: **serverless** (<b>au</b>to-<b>sca</b>les; <b>p</b>ay <b>f</b>or <b>da</b>ta <b>sto</b>red <b>a</b>nd <b>Elasti</b>Cache <b>Proce</b>ssing <b>Uni</b>ts [ECPUs]) <b>o</b>r **node-based clusters** (<b>cho</b>ose <b>no</b>de <b>ty</b>pe, <b>sha</b>rds, <b>repl</b>icas).
  - **Scaling node-based**: **vertical** (<b>cha</b>nge <b>no</b>de <b>ty</b>pe, <b>onl</b>ine <b>f</b>or <b>Red</b>is/<b>Val</b>key), **horizontal** (<b>a</b>dd/<b>rem</b>ove **shards** <b>wi</b>th <b>onl</b>ine <b>resha</b>rding <b>i</b>n <b>clus</b>ter <b>mo</b>de; <b>a</b>dd **read replicas**, <b>u</b>p <b>t</b>o 5 <b>p</b>er <b>sha</b>rd), **ElastiCache auto scaling** (<b>tar</b>get <b>trac</b>king <b>o</b>n CPU/<b>mem</b>ory <b>f</b>or <b>sha</b>rds <b>a</b>nd <b>repl</b>icas, <b>clus</b>ter <b>mo</b>de <b>enab</b>led).
  - **Key metrics**: `CPUUtilization` / **`EngineCPUUtilization`** (<b>Red</b>is/<b>Val</b>key <b>a</b>re <b>mos</b>tly <b>sin</b>gle-<b>thre</b>aded <b>p</b>er <b>sha</b>rd → <b>wat</b>ch <b>eng</b>ine CPU), `DatabaseMemoryUsagePercentage`, **`Evictions`** (<b>cac</b>he <b>fu</b>ll → <b>sca</b>le <b>u</b>p/<b>o</b>ut <b>o</b>r <b>tu</b>ne <b>Ti</b>me <b>T</b>o <b>Li</b>ve [TTL]), `CurrConnections`, `CacheHits`/`CacheMisses` (<b>h</b>it <b>rat</b>io), `ReplicationLag`, `SwapUsage`.
  - **Patterns**: [<b>la</b>zy <b>load</b>ing](00-cloudops-basics.md#lazy-loading-from-snapshots) (<b>cac</b>he-<b>asi</b>de), <b>wri</b>te-<b>thro</b>ugh, TTL, <b>sess</b>ion <b>sto</b>re.
  - **Global Datastore** (<b>cro</b>ss-<b>Reg</b>ion <b>replic</b>ation <b>f</b>or <b>Val</b>key/<b>Red</b>is).
- **Other caches**: **DynamoDB Accelerator (DAX)** <b>f</b>or <b>Dyna</b>moDB (<b>Ta</b>sk 2.1.3), **Application Programming Interface (API) Gateway caching** (<b>p</b>er <b>sta</b>ge), <b>re</b>ad <b>repl</b>icas.
- **Exam triggers:** "<b>red</b>uce DB <b>lo</b>ad <b>fr</b>om <b>repe</b>ated <b>rea</b>ds" → **ElastiCache**. "<b>Hi</b>gh `Evictions`" → <b>big</b>ger <b>nod</b>es/<b>mo</b>re <b>sha</b>rds <b>o</b>r <b>shor</b>ter <b>da</b>ta <b>life</b>time. "<b>O</b>ne <b>Red</b>is <b>no</b>de CPU <b>hi</b>gh <b>whi</b>le <b>oth</b>ers <b>id</b>le" → <b>h</b>ot <b>k</b>ey <b>o</b>r <b>n</b>ot <b>usi</b>ng <b>clus</b>ter <b>mo</b>de → **shard**. "<b>Sta</b>tic <b>ass</b>ets <b>over</b>load <b>w</b>eb <b>serv</b>ers" → **CloudFront**.

### Scaling managed databases (skill 2.1.3) `[also: SAA]`

- **Amazon RDS:**
  - **Vertical**: <b>mod</b>ify <b>inst</b>ance <b>cla</b>ss (<b>app</b>ly <b>immedi</b>ately <b>o</b>r <b>i</b>n <b>mainte</b>nance <b>win</b>dow; <b>bri</b>ef <b>down</b>time; **Multi-AZ** <b>redu</b>ces <b>i</b>t <b>t</b>o <b>a</b> <b>fail</b>over).
  - **Storage**: <b>incr</b>ease <b>si</b>ze/[<b>Inp</b>ut/<b>Out</b>put <b>Opera</b>tions <b>P</b>er <b>Sec</b>ond [IOPS]](00-cloudops-basics.md#iops-throughput-and-latency) <b>onl</b>ine; **storage autoscaling** (<b>wh</b>en <b>fr</b>ee <b>spa</b>ce < 10%, <b>f</b>or 5+ <b>minu</b>tes, <b>a</b>t <b>lea</b>st 6 <b>hou</b>rs <b>sin</b>ce <b>la</b>st <b>cha</b>nge; <b>s</b>et **maximum storage threshold**). <b>Stor</b>age <b>can</b>'t <b>b</b>e <b>decre</b>ased.
  - **Read replicas**: <b>u</b>p <b>t</b>o 15 (<b>MyS</b>QL, <b>Mari</b>aDB, <b>Postg</b>reSQL; <b>few</b>er <b>f</b>or <b>Ora</b>cle/<b>Struc</b>tured <b>Que</b>ry <b>Lang</b>uage [SQL] <b>Ser</b>ver), <b>asy</b>nc, <b>cro</b>ss-<b>Reg</b>ion, <b>promo</b>table; <b>moni</b>tor **`ReplicaLag`**; <b>a</b>pp <b>mu</b>st <b>rou</b>te <b>rea</b>ds.
  - **RDS Proxy**: <b>conne</b>ction <b>scal</b>ing.
  - **Blue/Green Deployments** <b>f</b>or <b>l</b>ow-<b>down</b>time <b>chan</b>ges (<b>eng</b>ine <b>upgr</b>ades, <b>param</b>eter <b>chan</b>ges) <b>wi</b>th <b>switc</b>hover.
- **Amazon Aurora:**
  - **Aurora Replicas** (<b>u</b>p <b>t</b>o 15, <b>sha</b>red <b>stor</b>age, <b>l</b>ow <b>l</b>ag) <b>beh</b>ind <b>t</b>he **reader endpoint**; **Aurora Auto Scaling** <b>ad</b>ds/<b>remo</b>ves <b>repl</b>icas <b>wi</b>th <b>tar</b>get <b>trac</b>king <b>o</b>n <b>aver</b>age CPU <b>o</b>r <b>connec</b>tions.
  - **Aurora Serverless v2**: <b>capa</b>city <b>i</b>n **ACUs** (<b>Aur</b>ora <b>capa</b>city <b>uni</b>ts; <b>ea</b>ch ≈ 2 <b>G</b>iB <b>mem</b>ory + CPU/<b>netw</b>ork), <b>s</b>et **min/max ACUs**, <b>sca</b>les <b>i</b>n <b>fi</b>ne <b>incre</b>ments <b>wit</b>hin <b>seco</b>nds; **min 0 ACUs = auto-pause** <b>wh</b>en <b>id</b>le; <b>m</b>ix <b>serve</b>rless <b>a</b>nd <b>provis</b>ioned <b>insta</b>nces <b>i</b>n <b>o</b>ne <b>clus</b>ter (<b>e</b>.g., <b>serve</b>rless <b>read</b>ers).
  - <b>Stor</b>age <b>au</b>to-<b>gro</b>ws (<b>u</b>p <b>t</b>o 128 <b>T</b>iB); **Aurora I/O-Optimized** <b>f</b>or <b>I</b>/<b>O</b>-<b>hea</b>vy <b>co</b>st; **Global Database** <b>f</b>or <b>cro</b>ss-<b>Reg</b>ion <b>re</b>ad <b>scal</b>ing.
- **Amazon DynamoDB:**
  - **On-demand** <b>mo</b>de: <b>insta</b>ntly <b>accomm</b>odates <b>traf</b>fic <b>u</b>p <b>t</b>o <b>dou</b>ble <b>t</b>he <b>prev</b>ious <b>pe</b>ak, <b>th</b>en <b>sca</b>les <b>furt</b>her; <b>p</b>ay <b>p</b>er <b>requ</b>est; <b>opti</b>onal **maximum throughput** <b>lim</b>its.
  - **Provisioned** <b>mo</b>de + **auto scaling** (<b>tar</b>get <b>trac</b>king <b>o</b>n <b>cons</b>umed/<b>provis</b>ioned <b>rat</b>io, <b>e</b>.g., 70%; <b>m</b>in/<b>m</b>ax RCU/WCU; <b>appl</b>ies <b>t</b>o <b>tab</b>les <b>a</b>nd **Global Secondary Indexes [GSIs]**; <b>rea</b>cts <b>i</b>n <b>minu</b>tes, <b>s</b>o <b>sud</b>den <b>spi</b>kes <b>c</b>an <b>sti</b>ll <b>thro</b>ttle; <b>bur</b>st <b>capa</b>city <b>hol</b>ds <b>u</b>p <b>t</b>o 300 <b>s</b> <b>o</b>f <b>unu</b>sed <b>capa</b>city).
  - <b>Swi</b>tch <b>betw</b>een <b>mod</b>es (<b>limi</b>ted <b>num</b>ber <b>o</b>f <b>tim</b>es <b>p</b>er 24 <b>hou</b>rs).
  - **Throttling causes**: **hot partition key**, <b>Glo</b>bal <b>Secon</b>dary <b>Ind</b>ex (GSI) <b>und</b>er-<b>provis</b>ioned (**GSI [throttling](00-cloudops-basics.md#throttling) throttles base table writes**), <b>excee</b>ding <b>p</b>er-<b>parti</b>tion <b>lim</b>its (3,000 <b>Re</b>ad <b>Capa</b>city <b>Un</b>it [RCU] / 1,000 <b>Wri</b>te <b>Capa</b>city <b>Un</b>it [WCU] <b>p</b>er <b>parti</b>tion).
  - **DAX**: <b>micros</b>econd <b>cac</b>hed <b>rea</b>ds, <b>redu</b>ces RCU <b>u</b>se (<b>event</b>ually <b>consi</b>stent <b>rea</b>ds <b>on</b>ly).
  - **Global tables** <b>f</b>or <b>mul</b>ti-<b>Reg</b>ion <b>sca</b>le; **Standard-Infrequent Access (Standard-IA) table class** <b>f</b>or <b>co</b>ld <b>tab</b>les.
  - <b>Metr</b>ics: `ConsumedReadCapacityUnits`, `ThrottledRequests`, `ReadThrottleEvents`/`WriteThrottleEvents`, `OnlineIndexThrottleEvents`; **CloudWatch Contributor Insights for DynamoDB** <b>fin</b>ds <b>h</b>ot <b>ke</b>ys.
- **Exam triggers:** "RDS <b>stor</b>age <b>nea</b>rly <b>fu</b>ll, <b>avo</b>id <b>man</b>ual <b>interv</b>ention" → **storage autoscaling**. "<b>Aur</b>ora <b>re</b>ad <b>traf</b>fic <b>fluct</b>uates" → **Aurora Auto Scaling replicas** <b>o</b>r <b>Serve</b>rless v2 <b>read</b>ers. "<b>Dyna</b>moDB <b>throt</b>tles <b>dur</b>ing <b>unpredi</b>ctable <b>spi</b>kes" → **on-demand**. "<b>Throt</b>tling <b>on</b>ly <b>o</b>n <b>cert</b>ain <b>ke</b>ys" → **hot partition**; <b>bet</b>ter <b>k</b>ey <b>des</b>ign/<b>wri</b>te <b>shar</b>ding. "<b>Wri</b>tes <b>throt</b>tled <b>tho</b>ugh <b>tab</b>le <b>h</b>as <b>capa</b>city" → **GSI under-provisioned**.

---

## Task 2.2: Highly available and resilient environments

### Elastic Load Balancing health checks (skill 2.2.1) `[also: SAA, DOP]`

- **Configuration (target group):** <b>prot</b>ocol (HTTP/HTTPS/TCP), [<b>po</b>rt](00-cloudops-basics.md#ports-and-protocols) (<b>traf</b>fic <b>po</b>rt <b>o</b>r <b>over</b>ride), **path** (`/health`), **success codes** (<b>Applic</b>ation <b>Lo</b>ad <b>Bala</b>ncer [ALB]: <b>e</b>.g., `200-299`), **interval** (5–300 <b>s</b>, <b>defa</b>ult 30), **timeout** (2–120 <b>s</b>), **healthy threshold** (<b>consec</b>utive <b>succe</b>sses, <b>defa</b>ult 5 ALB), **unhealthy threshold** (<b>consec</b>utive <b>fail</b>ures, <b>defa</b>ult 2).
- **Target states**: `initial` (<b>regist</b>ering), `healthy`, `unhealthy`, `unused` (<b>targ</b>et's AZ <b>n</b>ot <b>enab</b>led / <b>n</b>ot <b>us</b>ed), `draining` (<b>deregis</b>tration <b>del</b>ay, <b>defa</b>ult 300 <b>s</b>), `unavailable` (<b>hea</b>lth <b>che</b>cks <b>disa</b>bled).
- **Behavior**: ELB <b>rou</b>tes <b>on</b>ly <b>t</b>o <b>heal</b>thy <b>targ</b>ets; <b>i</b>f **all targets are unhealthy**, ALB/<b>Netw</b>ork <b>Lo</b>ad <b>Bala</b>ncer (NLB) **fail open** (<b>rou</b>te <b>t</b>o <b>a</b>ll) <b>rat</b>her <b>th</b>an <b>bla</b>ck-<b>hol</b>ing; ALB <b>retu</b>rns **[503](00-cloudops-basics.md#http-status-codes)** <b>wh</b>en <b>t</b>he <b>tar</b>get <b>gro</b>up <b>i</b>s <b>emp</b>ty.
- **Troubleshooting unhealthy targets:**
  1. **Security group** <b>o</b>n <b>t</b>he <b>tar</b>get <b>does</b>n't <b>all</b>ow <b>t</b>he <b>hea</b>lth <b>che</b>ck <b>po</b>rt **from the load balancer's SG** (<b>mo</b>st <b>com</b>mon).
  2. <b>Netw</b>ork <b>Acc</b>ess <b>Cont</b>rol <b>Lis</b>ts (NACLs) <b>blo</b>ck <b>hea</b>lth <b>che</b>ck <b>o</b>r <b>ephem</b>eral <b>ret</b>urn <b>por</b>ts.
  3. <b>Wro</b>ng **path/port**, <b>a</b>pp <b>retu</b>rns 301/302/401/404 → <b>adj</b>ust <b>pa</b>th <b>o</b>r <b>succ</b>ess <b>cod</b>es (ALB) <b>o</b>r <b>ma</b>ke `/health` <b>ret</b>urn 200.
  4. <b>A</b>pp <b>sta</b>rt <b>ti</b>me > <b>gra</b>ce <b>per</b>iod/<b>thres</b>holds; <b>depen</b>dency <b>che</b>cks <b>t</b>oo <b>str</b>ict (<b>hea</b>lth <b>che</b>ck <b>fai</b>ls <b>wh</b>en <b>t</b>he DB <b>i</b>s <b>sl</b>ow → <b>who</b>le <b>fle</b>et <b>unhea</b>lthy; <b>ke</b>ep "<b>shal</b>low" <b>v</b>s "<b>de</b>ep" <b>che</b>cks <b>i</b>n <b>mi</b>nd).
  5. <b>Time</b>out <b>t</b>oo <b>sho</b>rt <b>f</b>or <b>sl</b>ow <b>respo</b>nses.
  6. <b>Tar</b>get <b>i</b>n <b>a</b>n AZ <b>n</b>ot <b>enab</b>led <b>o</b>n <b>t</b>he LB (`unused`).
  7. <b>Che</b>ck **target health reason codes** (`Target.ResponseCodeMismatch`, `Target.Timeout`, `Target.FailedHealthChecks`, `Elb.InitialHealthChecking`) <b>i</b>n <b>t</b>he <b>cons</b>ole/`DescribeTargetHealth`.
- **ASG integration**: ASG <b>hea</b>lth <b>che</b>ck <b>ty</b>pe **ELB** <b>s</b>o <b>fail</b>ing <b>a</b>pp <b>insta</b>nces <b>g</b>et **replaced**, <b>n</b>ot <b>ju</b>st <b>remo</b>ved <b>fr</b>om <b>rota</b>tion.
- **Exam triggers:** "<b>targ</b>ets <b>sh</b>ow <b>unhea</b>lthy <b>b</b>ut <b>a</b>pp <b>wor</b>ks <b>wh</b>en <b>acce</b>ssed <b>dire</b>ctly" → **SG doesn't allow LB → health check port** <b>o</b>r <b>wro</b>ng <b>pa</b>th. "<b>Insta</b>nces <b>ke</b>ep <b>gett</b>ing <b>repl</b>aced <b>rig</b>ht <b>aft</b>er <b>lau</b>nch" → <b>rai</b>se **health check grace period**.

### Route 53 health checks (skill 2.2.1) `[also: ANS, SAA]`

- **Types:**
  - **Endpoint** <b>hea</b>lth <b>che</b>cks: <b>Hyper</b>text <b>Tran</b>sfer <b>Prot</b>ocol (HTTP), <b>Hyper</b>text <b>Tran</b>sfer <b>Prot</b>ocol <b>Sec</b>ure (HTTPS), <b>Transm</b>ission <b>Cont</b>rol <b>Prot</b>ocol (TCP) <b>fr</b>om **global health checkers** (≈15 <b>locat</b>ions); <b>opti</b>onal **string matching** (<b>fir</b>st 5,120 <b>byt</b>es <b>o</b>f <b>resp</b>onse); <b>inte</b>rval **30 s (standard)** <b>o</b>r **10 s (fast, extra cost)**; **failure threshold** (<b>defa</b>ult 3); <b>heal</b>thy <b>i</b>f ≥ 18% <b>o</b>f <b>chec</b>kers <b>rep</b>ort <b>heal</b>thy; <b>late</b>ncy <b>gra</b>phs <b>opti</b>onal.
  - **Calculated** <b>hea</b>lth <b>che</b>cks: <b>comb</b>ine <b>chi</b>ld <b>che</b>cks <b>wi</b>th AND/OR/<b>a</b>t-<b>lea</b>st-<b>N</b>.
  - **CloudWatch alarm**-<b>bas</b>ed: <b>hea</b>lth <b>foll</b>ows <b>a</b>n <b>alar</b>m's <b>sta</b>te; <b>u</b>se <b>f</b>or **private resources** (<b>i</b>n <b>a</b> <b>Virt</b>ual <b>Priv</b>ate <b>Clo</b>ud [VPC], <b>unreac</b>hable <b>b</b>y <b>inte</b>rnet <b>chec</b>kers) <b>a</b>nd <b>cus</b>tom <b>metr</b>ics.
- **Used with routing**: **failover** (<b>prim</b>ary/<b>secon</b>dary), <b>weig</b>hted, <b>late</b>ncy, <b>geoloc</b>ation, <b>multi</b>value: <b>unhea</b>lthy <b>reco</b>rds <b>a</b>re <b>skip</b>ped. **Alias records** <b>t</b>o AWS <b>resou</b>rces (ELB, <b>Cloud</b>Front) <b>u</b>se **Evaluate Target Health** <b>inst</b>ead <b>o</b>f <b>sepa</b>rate <b>che</b>cks.
- **Notifications**: <b>hea</b>lth <b>che</b>cks <b>publ</b>ish `HealthCheckStatus` <b>met</b>ric (<b>u</b>s-<b>ea</b>st-1) → <b>ala</b>rm → <b>Sim</b>ple <b>Notifi</b>cation <b>Serv</b>ice (SNS).
- **Troubleshooting:** <b>endpo</b>int's **firewall/SG/Network Access Control List (NACL) must allow Route 53 health checker Internet Protocol (IP) ranges** (<b>publi</b>shed <b>i</b>n `ip-ranges.json`, <b>serv</b>ice `ROUTE53_HEALTHCHECKS`); HTTPS <b>che</b>cks <b>don</b>'t <b>vali</b>date [<b>certif</b>icates](00-cloudops-basics.md#tls-and-certificates); <b>che</b>ck <b>fai</b>ls <b>i</b>f <b>resp</b>onse <b>i</b>s <b>slo</b>wer <b>th</b>an 4 <b>s</b> (<b>conne</b>ction) / 2 <b>s</b> (<b>fir</b>st <b>by</b>te) <b>o</b>r <b>str</b>ing <b>n</b>ot <b>fou</b>nd; <b>priv</b>ate IPs <b>can</b>'t <b>b</b>e <b>chec</b>ked <b>dire</b>ctly → <b>Cloud</b>Watch <b>ala</b>rm <b>che</b>ck; [<b>Dom</b>ain <b>Na</b>me <b>Sys</b>tem [DNS]](00-cloudops-basics.md#dns-records-and-ttl) <b>fail</b>over <b>del</b>ay = <b>inte</b>rval <b>×</b> <b>thres</b>hold + **record TTL** <b>cac</b>hed <b>b</b>y <b>resol</b>vers (<b>ke</b>ep TTL <b>l</b>ow, <b>e</b>.g., 60 <b>s</b>).
- **Amazon Application Recovery Controller (ARC)**: **readiness checks** (<b>ver</b>ify <b>stan</b>dby <b>h</b>as <b>matc</b>hing <b>capa</b>city/<b>quo</b>tas/<b>con</b>fig), **routing controls** (<b>hig</b>hly <b>avail</b>able <b>o</b>n/<b>o</b>ff <b>swit</b>ches <b>f</b>or <b>fail</b>over <b>v</b>ia <b>Rou</b>te 53 <b>hea</b>lth <b>che</b>cks, <b>contr</b>olled <b>fr</b>om <b>a</b> 5-<b>Reg</b>ion <b>clus</b>ter), **zonal shift** <b>a</b>nd **zonal autoshift** (<b>mo</b>ve <b>lo</b>ad <b>bala</b>ncer/ASG <b>traf</b>fic <b>aw</b>ay <b>fr</b>om <b>a</b>n <b>impa</b>ired AZ, <b>manu</b>ally <b>o</b>r <b>automat</b>ically <b>b</b>y AWS), **Region switch** (<b>orches</b>trated <b>mul</b>ti-<b>Reg</b>ion <b>fail</b>over <b>pla</b>ns).
- **Exam triggers:** "<b>fa</b>il <b>ov</b>er <b>t</b>o DR <b>Reg</b>ion <b>wh</b>en <b>prim</b>ary <b>unhea</b>lthy" → **failover routing + health check**. "<b>Hea</b>lth <b>che</b>ck <b>a</b> <b>reso</b>urce <b>i</b>n <b>a</b> [<b>priv</b>ate <b>sub</b>net](00-cloudops-basics.md#public-vs-private-subnet)" → **CloudWatch alarm health check**. "<b>Hea</b>lth <b>che</b>cks <b>fa</b>il <b>alth</b>ough <b>t</b>he <b>si</b>te <b>wor</b>ks" → <b>fire</b>wall <b>blo</b>cks **Route 53 checker IPs**. "<b>Shi</b>ft <b>traf</b>fic <b>aw</b>ay <b>fr</b>om <b>a</b> <b>degr</b>aded AZ" → **ARC zonal shift**.

### Multi-AZ and fault-tolerant configuration (skill 2.2.2) `[also: SAA]`

| <b>Compo</b>nent | <b>Mul</b>ti-AZ / <b>fau</b>lt-<b>tole</b>rant <b>configu</b>ration |
|---|---|
| EC2 <b>comp</b>ute | ASG <b>acr</b>oss ≥ 2 (<b>idea</b>lly 3) AZs <b>beh</b>ind ELB; <b>ena</b>ble <b>cro</b>ss-<b>zo</b>ne LB; <b>sta</b>tic <b>stabi</b>lity (<b>eno</b>ugh <b>capa</b>city <b>i</b>f <b>o</b>ne AZ <b>fai</b>ls) |
| <b>Lo</b>ad <b>balan</b>cers | <b>Ena</b>ble <b>subn</b>ets <b>i</b>n <b>mult</b>iple AZs (ALB <b>requ</b>ires ≥ 2) |
| <b>Netw</b>ork <b>Addr</b>ess <b>Transl</b>ation (NAT) | **One NAT gateway per AZ**; <b>ea</b>ch <b>priv</b>ate [<b>rou</b>te <b>tab</b>le](00-cloudops-basics.md#route-table-and-longest-prefix-match) → <b>i</b>ts <b>o</b>wn <b>AZ</b>'s NAT |
| RDS | **Multi-AZ DB instance** (<b>synchr</b>onous <b>stan</b>dby, <b>fail</b>over 60–120 <b>s</b>, <b>stan</b>dby <b>n</b>ot <b>read</b>able) <b>o</b>r **Multi-AZ DB cluster** (2 <b>read</b>able <b>stan</b>dbys, <b>fail</b>over <b>typic</b>ally < 35 <b>s</b>) |
| <b>Aur</b>ora | <b>Repl</b>icas <b>i</b>n <b>oth</b>er AZs (<b>fail</b>over ~30 <b>s</b>, <b>prio</b>rity <b>tie</b>rs 0–15); <b>stor</b>age <b>alre</b>ady 6 <b>cop</b>ies / 3 AZs |
| <b>Elasti</b>Cache | <b>Replic</b>ation <b>gro</b>up <b>wi</b>th **Multi-AZ + automatic failover** (<b>repl</b>ica <b>prom</b>oted) |
| <b>Dyna</b>moDB, S3, <b>Sim</b>ple <b>Que</b>ue <b>Serv</b>ice (SQS), SNS, <b>Lam</b>bda, <b>Elas</b>tic <b>Fi</b>le <b>Sys</b>tem (EFS) <b>Regi</b>onal | <b>Mul</b>ti-AZ <b>b</b>y <b>defa</b>ult |
| EFS | <b>Regi</b>onal <b>fi</b>le <b>sys</b>tem + <b>mou</b>nt <b>tar</b>get <b>i</b>n <b>ea</b>ch AZ |
| <b>F</b>Sx | **Multi-AZ** <b>deplo</b>yment <b>ty</b>pe (<b>Wind</b>ows, ONTAP, <b>Open</b>ZFS); <b>Lus</b>tre <b>i</b>s <b>sin</b>gle-AZ |
| <b>Reds</b>hift | <b>Mul</b>ti-AZ (RA3) <b>o</b>r <b>snaps</b>hots + <b>reloc</b>ation |
| <b>OpenS</b>earch | <b>Mul</b>ti-AZ <b>wi</b>th <b>stan</b>dby / <b>zo</b>ne <b>aware</b>ness, 3 <b>dedic</b>ated <b>mas</b>ter <b>nod</b>es |
| <b>Virt</b>ual <b>Priv</b>ate <b>Netw</b>ork (VPN) | <b>T</b>wo <b>tunn</b>els <b>p</b>er <b>conne</b>ction; <b>sec</b>ond <b>cust</b>omer <b>gate</b>way <b>dev</b>ice |
| <b>Dir</b>ect <b>Conn</b>ect | <b>T</b>wo <b>connec</b>tions <b>i</b>n <b>diffe</b>rent <b>Dir</b>ect <b>Conn</b>ect (DX) <b>locat</b>ions; VPN <b>bac</b>kup |
| EBS | AZ-<b>sco</b>ped → <b>snaps</b>hots/AWS <b>Bac</b>kup <b>f</b>or <b>reco</b>very <b>elsew</b>here |

- **RDS Multi-AZ failover triggers**: AZ <b>out</b>age, <b>prim</b>ary <b>inst</b>ance <b>fail</b>ure, <b>stor</b>age <b>fail</b>ure, <b>Opera</b>ting <b>Sys</b>tem (OS) <b>patc</b>hing/<b>mainte</b>nance, <b>inst</b>ance <b>cla</b>ss <b>cha</b>nge, <b>man</b>ual **reboot with failover**. <b>T</b>he **DNS endpoint stays the same** (<b>ap</b>ps <b>mu</b>st <b>recon</b>nect; <b>ke</b>ep DNS TTL <b>cach</b>ing <b>l</b>ow <b>i</b>n <b>clie</b>nts; RDS <b>Pro</b>xy <b>smoo</b>ths <b>i</b>t). <b>Eve</b>nt `RDS-EVENT-0049` <b>e</b>tc. <b>v</b>ia RDS <b>eve</b>nt <b>subscri</b>ptions.
- **Convert Single-AZ → Multi-AZ**: <b>mod</b>ify <b>t</b>he <b>inst</b>ance (<b>n</b>o <b>down</b>time; <b>snap</b>shot <b>tak</b>en <b>a</b>nd <b>stan</b>dby <b>bui</b>lt; <b>so</b>me <b>I</b>/<b>O</b> <b>imp</b>act).
- **Testing**: <b>reb</b>oot <b>wi</b>th <b>fail</b>over; **AWS Fault Injection Service (FIS)** <b>experi</b>ments (AZ <b>pow</b>er <b>interr</b>uption <b>scen</b>ario, <b>st</b>op <b>insta</b>nces, <b>thro</b>ttle <b>Applic</b>ation <b>Progra</b>mming <b>Inter</b>faces [APIs]); ARC <b>zon</b>al <b>shi</b>ft <b>dri</b>lls.
- **Exam triggers:** "<b>data</b>base <b>mu</b>st <b>st</b>ay <b>avail</b>able <b>i</b>f <b>a</b>n AZ <b>fai</b>ls, <b>autom</b>atic" → **RDS Multi-AZ**. "<b>Te</b>st <b>th</b>at <b>fail</b>over <b>wor</b>ks" → **reboot with failover** / FIS. "<b>Priv</b>ate <b>subn</b>ets <b>lo</b>se <b>inte</b>rnet <b>wh</b>en <b>o</b>ne AZ <b>fai</b>ls" → <b>sin</b>gle NAT <b>gate</b>way → **NAT per AZ**.

---

## Task 2.3: Backup and restore strategies

### AWS Backup (skill 2.3.1) `[also: DOP, SAA]`

- **What it is & key terms:**
  - **Backup plan**: <b>o</b>ne <b>o</b>r <b>mo</b>re **backup rules** (<b>sche</b>dule <b>cr</b>on/<b>frequ</b>ency <b>do</b>wn <b>t</b>o <b>hou</b>rly, **backup window** <b>sta</b>rt + <b>compl</b>etion <b>win</b>dow, **lifecycle**: <b>trans</b>ition <b>t</b>o **cold storage** <b>aft</b>er <b>N</b> <b>da</b>ys <b>a</b>nd <b>del</b>ete <b>aft</b>er <b>M</b> <b>da</b>ys; <b>co</b>ld <b>stor</b>age <b>nee</b>ds ≥ 90 <b>da</b>ys <b>the</b>re), **continuous backup** <b>f</b>or <b>Poi</b>nt-<b>I</b>n-<b>Ti</b>me <b>Reco</b>very [PITR] (RDS, <b>Aur</b>ora, S3, SAP HANA <b>o</b>n EC2; <b>u</b>p <b>t</b>o 35 <b>da</b>ys), **copy rules** <b>t</b>o <b>anot</b>her <b>Reg</b>ion/<b>acco</b>unt, <b>ta</b>gs.
  - **Resource assignment**: <b>b</b>y **tags** (<b>e</b>.g., `backup=daily`) <b>o</b>r <b>reso</b>urce IDs/<b>typ</b>es; <b>us</b>es <b>a</b>n **IAM service role** (`AWSBackupDefaultServiceRole`).
  - **Backup vault**: <b>encry</b>pted <b>conta</b>iner <b>f</b>or **recovery points** (KMS <b>k</b>ey <b>p</b>er <b>vau</b>lt; <b>f</b>or <b>ma</b>ny <b>serv</b>ices <b>t</b>he <b>bac</b>kup <b>us</b>es <b>t</b>he <b>sour</b>ce's <b>encry</b>ption); **vault access policy** (<b>e</b>.g., <b>de</b>ny `DeleteRecoveryPoint`).
  - **Backup Vault Lock**: <b>Wri</b>te <b>On</b>ce, <b>Re</b>ad <b>Ma</b>ny (WORM): **governance mode** (<b>privi</b>leged <b>use</b>rs <b>c</b>an <b>rem</b>ove) <b>o</b>r **[compliance](00-cloudops-basics.md#compliance-as-code--continuous-compliance) mode** ([<b>immut</b>able](00-cloudops-basics.md#immutable-vs-mutable-infrastructure) <b>aft</b>er <b>t</b>he <b>co</b>ol-<b>o</b>ff <b>gra</b>ce <b>per</b>iod, <b>m</b>in 72 <b>h</b>); <b>m</b>in/<b>m</b>ax <b>reten</b>tion <b>enfo</b>rced.
  - **Logically air-gapped vault**: <b>isol</b>ated, AWS-<b>own</b>ed-<b>k</b>ey-<b>encry</b>pted <b>vau</b>lt <b>th</b>at <b>c</b>an <b>b</b>e <b>sha</b>red <b>t</b>o <b>a</b> <b>reco</b>very <b>acco</b>unt <b>v</b>ia RAM.
  - **Cross-Region / cross-account copy** (<b>Organiz</b>ations), **organization backup policies** (<b>dep</b>loy <b>pla</b>ns <b>t</b>o <b>a</b>ll <b>acco</b>unts <b>fr</b>om <b>t</b>he <b>manag</b>ement <b>acco</b>unt).
  - **Backup Audit Manager**: <b>frame</b>works <b>o</b>f <b>cont</b>rols (<b>e</b>.g., <b>resou</b>rces <b>prote</b>cted <b>b</b>y <b>a</b> <b>pl</b>an, <b>mini</b>mum <b>frequ</b>ency/<b>reten</b>tion, <b>cro</b>ss-<b>Reg</b>ion <b>co</b>py, <b>encry</b>ption) <b>wi</b>th <b>repo</b>rts.
  - **Restore testing**: <b>sched</b>uled <b>autom</b>atic <b>rest</b>ores <b>t</b>o <b>vali</b>date <b>recover</b>ability <b>a</b>nd <b>meas</b>ure <b>rest</b>ore <b>ti</b>me.
  - **Supported**: EC2 (AMI-<b>bas</b>ed), EBS, RDS, <b>Aur</b>ora, <b>Dyna</b>moDB, EFS, <b>F</b>Sx (<b>a</b>ll <b>typ</b>es), S3, <b>Stor</b>age <b>Gate</b>way <b>volu</b>mes, <b>Docum</b>entDB, <b>Nept</b>une, <b>Reds</b>hift, <b>Times</b>tream, <b>CloudFo</b>rmation <b>sta</b>cks, SAP HANA, <b>VMw</b>are <b>Virt</b>ual <b>Mach</b>ines (VMs), EKS (<b>new</b>er), <b>a</b>nd <b>mo</b>re.
  - **Notifications**: <b>vau</b>lt <b>eve</b>nts <b>t</b>o SNS; <b>j</b>ob <b>sta</b>te <b>t</b>o <b>EventB</b>ridge; <b>j</b>ob <b>monit</b>oring <b>i</b>n <b>cons</b>ole/<b>Cloud</b>Watch <b>metr</b>ics (`NumberOfBackupJobsFailed`).
- **Native alternatives:**
  - **EBS**: <b>snaps</b>hots (<b>increm</b>ental); **Amazon Data Lifecycle Manager (DLM)** <b>poli</b>cies <b>t</b>o <b>cre</b>ate/<b>ret</b>ain/<b>co</b>py (<b>cro</b>ss-<b>Reg</b>ion, <b>cro</b>ss-<b>acco</b>unt) <b>snaps</b>hots <b>a</b>nd **EBS-backed AMIs** <b>o</b>n <b>sched</b>ules, <b>b</b>y <b>ta</b>gs; **Fast Snapshot Restore**; **Snapshot Archive**; **Recycle Bin** <b>rul</b>es (<b>reco</b>ver <b>dele</b>ted <b>snaps</b>hots/AMIs <b>wit</b>hin <b>reten</b>tion); **multi-volume crash-consistent snapshots** <b>o</b>f <b>a</b>n <b>inst</b>ance; <b>applic</b>ation-<b>consi</b>stent <b>snaps</b>hots <b>v</b>ia <b>Vol</b>ume <b>Sha</b>dow <b>Co</b>py <b>Serv</b>ice [VSS] (<b>Wind</b>ows) / <b>p</b>re-<b>po</b>st <b>scri</b>pts <b>i</b>n DLM <b>o</b>r <b>Bac</b>kup.
  - **RDS**: <b>autom</b>ated <b>back</b>ups (<b>dai</b>ly <b>snap</b>shot + <b>transa</b>ction <b>lo</b>gs <b>eve</b>ry 5 <b>m</b>in; <b>reten</b>tion 0–35 <b>da</b>ys; 0 <b>disa</b>bles <b>a</b>nd <b>dele</b>tes <b>th</b>em), **manual snapshots** (<b>ke</b>pt <b>unt</b>il <b>dele</b>ted; <b>co</b>py <b>cro</b>ss-<b>Reg</b>ion/<b>acco</b>unt; <b>sha</b>re; <b>encry</b>pted <b>snaps</b>hots <b>ne</b>ed KMS <b>k</b>ey <b>shar</b>ing), **automated backup replication** <b>t</b>o <b>anot</b>her <b>Reg</b>ion, **export snapshot to S3** (<b>Parq</b>uet).
  - **Aurora**: <b>conti</b>nuous <b>back</b>ups (1–35 <b>da</b>ys), <b>man</b>ual <b>clus</b>ter <b>snaps</b>hots, **Backtrack** (<b>Aur</b>ora <b>MyS</b>QL), **cloning**.
  - **DynamoDB**: **PITR** (<b>conti</b>nuous, 1–35 <b>da</b>ys <b>config</b>urable <b>reco</b>very <b>per</b>iod), **on-demand backups** (<b>ke</b>pt <b>unt</b>il <b>dele</b>ted), **export to S3**.
  - **S3**: **versioning**, **replication** (CRR/<b>Sa</b>me-<b>Reg</b>ion <b>Replic</b>ation [SRR], <b>Replic</b>ation <b>Ti</b>me <b>Cont</b>rol [RTC]), **Object Lock**, AWS <b>Bac</b>kup <b>f</b>or S3 (<b>conti</b>nuous + <b>peri</b>odic).
  - **EC2**: <b>cre</b>ate AMI (`CreateImage`, <b>reb</b>oot <b>v</b>s <b>n</b>o-<b>reb</b>oot <b>consis</b>tency), DLM AMI <b>poli</b>cies.
- **Exam triggers:** "<b>centr</b>ally <b>man</b>age <b>back</b>ups <b>f</b>or EBS, RDS, <b>Dyna</b>moDB <b>a</b>nd EFS <b>wi</b>th <b>reten</b>tion <b>poli</b>cies <b>acr</b>oss <b>acco</b>unts" → **AWS Backup** (+ <b>organi</b>zation <b>bac</b>kup <b>poli</b>cies). "<b>Back</b>ups <b>mu</b>st <b>b</b>e <b>immut</b>able <b>ev</b>en <b>t</b>o <b>adm</b>ins" → **Vault Lock compliance mode**. "<b>Pro</b>ve <b>back</b>ups <b>me</b>et <b>pol</b>icy" → **Backup Audit Manager**. "<b>Auto</b>mate EBS <b>snaps</b>hots <b>b</b>y <b>t</b>ag <b>chea</b>ply" → **DLM**. "<b>Reco</b>ver <b>a</b> <b>snap</b>shot <b>some</b>one <b>dele</b>ted" → **Recycle Bin** (<b>i</b>f <b>a</b> <b>ru</b>le <b>exis</b>ted).

### Restoring databases to meet RTO, RPO and cost (skill 2.3.2) `[also: SAA]`

| <b>Met</b>hod | RPO | RTO | <b>Not</b>es |
|---|---|---|---|
| **RDS point-in-time restore** | ~5 <b>minu</b>tes (<b>lat</b>est <b>resto</b>rable <b>ti</b>me) | <b>Minu</b>tes <b>t</b>o <b>hou</b>rs (<b>si</b>ze <b>depen</b>dent) | <b>Rest</b>ores <b>t</b>o <b>a</b> **new DB instance** (<b>n</b>ew <b>endp</b>oint) <b>a</b>ny <b>sec</b>ond <b>wit</b>hin <b>reten</b>tion; <b>upd</b>ate <b>a</b>pp <b>conne</b>ction <b>o</b>r <b>ren</b>ame <b>insta</b>nces |
| **RDS snapshot restore** | <b>Ti</b>me <b>o</b>f <b>snap</b>shot | <b>Minu</b>tes–<b>hou</b>rs | <b>N</b>ew <b>inst</b>ance; <b>cho</b>ose <b>param</b>eter/<b>opt</b>ion <b>gro</b>ups, SGs; <b>rest</b>ored <b>volu</b>mes <b>la</b>zy-<b>lo</b>ad <b>fr</b>om S3 |
| **Promote read replica** | <b>Seco</b>nds (<b>repl</b>ica <b>l</b>ag) | <b>Minu</b>tes | <b>Bre</b>aks <b>replic</b>ation; <b>beco</b>mes <b>stand</b>alone <b>wri</b>ter (<b>cro</b>ss-<b>Reg</b>ion DR) |
| **Multi-AZ failover** | 0 | 1–2 <b>m</b>in | <b>N</b>ot <b>a</b> <b>bac</b>kup (<b>corru</b>ption/<b>dele</b>tes <b>repli</b>cate) |
| **Aurora PITR / snapshot** | ~<b>seco</b>nds–5 <b>m</b>in | <b>Minu</b>tes (<b>n</b>ew <b>clus</b>ter; <b>a</b>dd <b>insta</b>nces) | |
| **Aurora Backtrack** | <b>Seco</b>nds | <b>Minu</b>tes, **in place** | <b>Aur</b>ora <b>MyS</b>QL <b>on</b>ly; <b>u</b>p <b>t</b>o 72 <b>h</b> <b>win</b>dow; <b>n</b>o <b>n</b>ew <b>clus</b>ter |
| **Aurora cloning** | <b>n</b>/<b>a</b> | <b>Minu</b>tes | <b>Co</b>py-<b>o</b>n-<b>wri</b>te <b>te</b>st <b>cop</b>ies (<b>invest</b>igate <b>with</b>out <b>impac</b>ting <b>pr</b>od) |
| **Aurora Global Database failover/switchover** | ~1 <b>s</b> (<b>switc</b>hover 0) | ~1 <b>m</b>in | <b>Cro</b>ss-<b>Reg</b>ion DR |
| **DynamoDB PITR** | <b>Seco</b>nds (<b>t</b>o <b>a</b>ny <b>sec</b>ond <b>i</b>n <b>win</b>dow) | <b>Minu</b>tes–<b>hou</b>rs | <b>Rest</b>ores <b>t</b>o <b>a</b> **new table** (<b>r</b>e-<b>cre</b>ate <b>au</b>to <b>scal</b>ing, TTL, <b>stre</b>ams, <b>ta</b>gs, IAM <b>poli</b>cies <b>manu</b>ally); <b>sa</b>me <b>o</b>r <b>oth</b>er <b>Reg</b>ion |
| **DynamoDB on-demand backup restore** | <b>Bac</b>kup <b>ti</b>me | <b>Minu</b>tes–<b>hou</b>rs | <b>N</b>ew <b>tab</b>le |
| **AWS Backup restore** | <b>P</b>er <b>pl</b>an | <b>P</b>er <b>reso</b>urce | <b>Rest</b>ore <b>test</b>ing <b>meas</b>ures <b>i</b>t |

- **Restore gotchas**: <b>rest</b>ored RDS <b>insta</b>nces <b>g</b>et <b>t</b>he **default security group/parameter group** <b>unl</b>ess <b>speci</b>fied; <b>encry</b>pted <b>snap</b>shot <b>rest</b>ore <b>nee</b>ds <b>acc</b>ess <b>t</b>o <b>t</b>he KMS <b>k</b>ey; <b>y</b>ou <b>can</b>'t <b>rest</b>ore <b>in</b>to <b>a</b>n <b>exis</b>ting <b>inst</b>ance (<b>cre</b>ate <b>n</b>ew, <b>th</b>en <b>swi</b>tch <b>endpo</b>ints <b>o</b>r <b>u</b>se DNS <b>Canon</b>ical <b>Na</b>me [CNAME]); <b>dele</b>ted DB <b>wi</b>th <b>autom</b>ated <b>back</b>ups → <b>reta</b>ined <b>autom</b>ated <b>back</b>ups (<b>i</b>f <b>cho</b>sen) <b>o</b>r <b>fin</b>al <b>snap</b>shot.
- **Cost choices**: <b>snap</b>shot <b>frequ</b>ency <b>v</b>s RPO; <b>co</b>ld <b>stor</b>age <b>tie</b>rs <b>f</b>or <b>lo</b>ng <b>reten</b>tion; <b>cro</b>ss-<b>Reg</b>ion <b>cop</b>ies <b>on</b>ly <b>whe</b>re DR <b>nee</b>ds <b>th</b>em; PITR <b>co</b>st <b>p</b>er GB.
- **Exam triggers:** "<b>accid</b>ental <b>del</b>ete <b>o</b>f <b>ro</b>ws 2 <b>hou</b>rs <b>a</b>go, <b>mini</b>mal <b>da</b>ta <b>lo</b>ss" → **PITR to just before the event** (<b>n</b>ew <b>inst</b>ance), <b>th</b>en <b>co</b>py <b>da</b>ta <b>ba</b>ck <b>o</b>r <b>c</b>ut <b>ov</b>er. "<b>Un</b>do <b>a</b> <b>b</b>ad <b>cha</b>nge <b>o</b>n <b>Aur</b>ora <b>MyS</b>QL <b>quic</b>kly <b>with</b>out <b>a</b> <b>n</b>ew <b>clus</b>ter" → **Backtrack**. "<b>Te</b>st <b>a</b> <b>f</b>ix <b>o</b>n <b>produ</b>ction-<b>li</b>ke <b>da</b>ta <b>with</b>out <b>affec</b>ting <b>pr</b>od" → **Aurora clone**.

### Versioning for storage services (skill 2.3.3) `[also: SAA]`

- **Amazon S3 versioning**:
  - <b>Buc</b>ket <b>sta</b>tes: **unversioned → enabled → suspended** (<b>can</b>'t <b>ret</b>urn <b>t</b>o <b>unvers</b>ioned).
  - <b>Eve</b>ry <b>overw</b>rite <b>crea</b>tes <b>a</b> <b>n</b>ew <b>vers</b>ion ID; **delete** <b>ad</b>ds <b>a</b> **delete marker** (<b>obj</b>ect <b>appe</b>ars <b>go</b>ne; <b>del</b>ete <b>t</b>he <b>mar</b>ker <b>t</b>o <b>rest</b>ore); <b>perman</b>ently <b>dele</b>ting <b>a</b> <b>vers</b>ion <b>requ</b>ires <b>speci</b>fying <b>t</b>he <b>vers</b>ion ID.
  - **[Multi-Factor Authentication [MFA]](00-cloudops-basics.md#mfa) Delete** (<b>ro</b>ot-<b>on</b>ly, <b>Comm</b>and <b>Li</b>ne <b>Inter</b>face [CLI]) <b>f</b>or <b>ext</b>ra <b>prote</b>ction.
  - <b>Cos</b>ts: <b>eve</b>ry <b>vers</b>ion <b>i</b>s <b>bil</b>led → **lifecycle rules for noncurrent versions** (<b>trans</b>ition/<b>exp</b>ire <b>aft</b>er <b>N</b> <b>da</b>ys, <b>ke</b>ep <b>t</b>he <b>lat</b>est <b>N</b> <b>noncu</b>rrent <b>vers</b>ions) <b>a</b>nd **expired object delete marker** <b>clea</b>nup.
  - <b>Requ</b>ired <b>f</b>or **replication**, **Object Lock**.
  - <b>Rest</b>ore <b>a</b>n <b>o</b>ld <b>vers</b>ion: <b>co</b>py <b>th</b>at <b>vers</b>ion <b>ov</b>er <b>t</b>he <b>curr</b>ent <b>obj</b>ect, <b>o</b>r <b>del</b>ete <b>new</b>er <b>vers</b>ions/<b>mark</b>ers.
- **Amazon FSx versioning features**:
  - **FSx for Windows File Server**: **shadow copies** (<b>Wind</b>ows <b>Vol</b>ume <b>Sha</b>dow <b>Co</b>py <b>Serv</b>ice <b>snaps</b>hots <b>o</b>n <b>a</b> <b>sche</b>dule; <b>use</b>rs <b>rest</b>ore <b>prev</b>ious <b>vers</b>ions <b>o</b>f <b>fil</b>es <b>v</b>ia <b>Wind</b>ows <b>Expl</b>orer "<b>Prev</b>ious <b>Vers</b>ions"), <b>pl</b>us <b>autom</b>atic <b>dai</b>ly <b>back</b>ups <b>a</b>nd <b>us</b>er-<b>initi</b>ated <b>back</b>ups.
  - **FSx for NetApp ONTAP**: **volume snapshots** (<b>snap</b>shot <b>poli</b>cies; <b>use</b>rs <b>bro</b>wse `.snapshot` <b>direct</b>ories), **FlexClone**, <b>SnapM</b>irror <b>replic</b>ation.
  - **FSx for OpenZFS**: <b>snaps</b>hots <b>a</b>nd <b>clo</b>nes.
  - **FSx for Lustre**: <b>back</b>ups <b>f</b>or <b>persi</b>stent <b>fi</b>le <b>syst</b>ems; <b>da</b>ta <b>versi</b>oning <b>v</b>ia <b>t</b>he <b>lin</b>ked S3 <b>buck</b>et's <b>versi</b>oning.
- **EFS**: <b>n</b>o <b>fi</b>le <b>versi</b>oning → <b>u</b>se AWS <b>Bac</b>kup (<b>increm</b>ental, PITR-<b>li</b>ke <b>dai</b>ly) <b>o</b>r EFS <b>replic</b>ation (<b>replic</b>ation <b>isn</b>'t <b>versi</b>oning).
- **Exam triggers:** "<b>use</b>rs <b>accide</b>ntally <b>overw</b>rite S3 <b>obje</b>cts <b>a</b>nd <b>ne</b>ed <b>t</b>o <b>reco</b>ver <b>th</b>em" → **versioning**. "<b>Versi</b>oned <b>buc</b>ket <b>stor</b>age <b>kee</b>ps <b>grow</b>ing" → **noncurrent version expiration**. "<b>Wind</b>ows <b>use</b>rs <b>rest</b>ore <b>the</b>ir <b>o</b>wn <b>prev</b>ious <b>fi</b>le <b>vers</b>ions" → **FSx for Windows shadow copies**.

### Disaster recovery procedures and best practices (skill 2.3.4) `[also: SAA]`

| <b>Stra</b>tegy | DR <b>Reg</b>ion <b>cont</b>ents | RPO / RTO | <b>Operat</b>ional <b>proce</b>dure |
|---|---|---|---|
| **Backup and restore** | <b>Back</b>ups <b>on</b>ly (AWS <b>Bac</b>kup <b>cop</b>ies, <b>snaps</b>hots/AMIs <b>cop</b>ied, S3 <b>Cro</b>ss-<b>Reg</b>ion <b>Replic</b>ation [CRR]) | <b>Hou</b>rs / <b>hou</b>rs–<b>d</b>ay | <b>Rest</b>ore <b>da</b>ta, <b>dep</b>loy <b>inf</b>ra <b>fr</b>om [<b>Infrast</b>ructure <b>a</b>s <b>Co</b>de [<b>I</b>aC]](00-cloudops-basics.md#infrastructure-as-code-iac) (<b>CloudFo</b>rmation/<b>Stack</b>Sets), <b>upd</b>ate DNS |
| **Pilot light** | <b>Da</b>ta <b>repli</b>cated <b>li</b>ve (RDS <b>cro</b>ss-<b>Reg</b>ion <b>repl</b>ica, <b>Aur</b>ora <b>Glo</b>bal DB, S3 CRR, <b>Dyna</b>moDB <b>glo</b>bal <b>tab</b>le); <b>co</b>re <b>inf</b>ra <b>depl</b>oyed <b>b</b>ut **compute off/zero** | <b>Minu</b>tes / <b>te</b>ns <b>o</b>f <b>minu</b>tes | <b>Prom</b>ote DB, <b>sca</b>le ASG <b>fr</b>om 0, <b>swi</b>tch <b>Rou</b>te 53 |
| **Warm standby** | **Scaled-down full stack running** | <b>Seco</b>nds–<b>minu</b>tes / <b>minu</b>tes | <b>Sca</b>le <b>u</b>p, <b>fa</b>il <b>ov</b>er DNS/<b>Glo</b>bal <b>Accele</b>rator |
| **Active/active (multi-site)** | <b>Fu</b>ll <b>capa</b>city <b>serv</b>ing <b>traf</b>fic | <b>Ne</b>ar <b>ze</b>ro / <b>ne</b>ar <b>ze</b>ro | <b>Rou</b>te 53 <b>late</b>ncy/<b>weig</b>hted + <b>hea</b>lth <b>che</b>cks, <b>glo</b>bal <b>tab</b>les, <b>Aur</b>ora <b>Glo</b>bal DB <b>wi</b>th <b>wri</b>te <b>forwa</b>rding |

- **Best practices**:
  - <b>Def</b>ine RPO/RTO <b>p</b>er <b>work</b>load; <b>cho</b>ose <b>t</b>he <b>chea</b>pest <b>stra</b>tegy <b>th</b>at <b>mee</b>ts <b>th</b>em.
  - <b>Ke</b>ep <b>I</b>aC <b>a</b>nd AMIs <b>rea</b>dy <b>i</b>n <b>t</b>he DR <b>Reg</b>ion (**AMI copy**, **Elastic Container Registry [ECR] replication**, **CloudFormation StackSets**, <b>Param</b>eter <b>Sto</b>re/<b>Secr</b>ets <b>Mana</b>ger **replication** <b>o</b>f <b>secr</b>ets).
  - <b>P</b>re-<b>rai</b>se **service quotas** <b>i</b>n <b>t</b>he DR <b>Reg</b>ion; **Capacity Reservations** <b>f</b>or <b>crit</b>ical <b>capa</b>city.
  - <b>U</b>se **AWS Elastic Disaster Recovery** <b>f</b>or <b>serv</b>ers (<b>conti</b>nuous <b>blo</b>ck <b>replic</b>ation <b>t</b>o <b>a</b> <b>l</b>ow-<b>co</b>st <b>stag</b>ing <b>ar</b>ea, <b>lau</b>nch <b>reco</b>very <b>insta</b>nces <b>i</b>n <b>minu</b>tes, <b>fail</b>back).
  - **Test regularly** (<b>ga</b>me <b>da</b>ys, FIS, AWS <b>Bac</b>kup <b>rest</b>ore <b>test</b>ing, ARC <b>readi</b>ness <b>che</b>cks); <b>docu</b>ment [<b>runb</b>ooks](00-cloudops-basics.md#runbook-and-playbook) (<b>Syst</b>ems <b>Mana</b>ger [SSM] <b>Autom</b>ation, ARC <b>Reg</b>ion <b>swi</b>tch <b>pla</b>ns).
  - <b>Prot</b>ect <b>back</b>ups <b>fr</b>om <b>t</b>he <b>sa</b>me <b>bla</b>st <b>rad</b>ius: **separate account + Region**, <b>Vau</b>lt <b>Lo</b>ck, <b>logic</b>ally <b>a</b>ir-<b>gap</b>ped <b>vau</b>lts.
  - <b>Moni</b>tor <b>replic</b>ation: `ReplicaLag`, S3 <b>replic</b>ation <b>metr</b>ics (`ReplicationLatency`, `OperationsPendingReplication`), <b>Aur</b>ora `AuroraGlobalDBReplicationLag`, <b>Dyna</b>moDB `ReplicationLatency`.
- **Exam triggers:** "<b>low</b>est <b>co</b>st DR, RTO 24 <b>h</b>" → **backup and restore**. "RPO <b>minu</b>tes, RTO < 1 <b>h</b>, <b>mini</b>mize <b>co</b>st" → **pilot light**. "RTO <b>minu</b>tes, <b>so</b>me <b>traf</b>fic <b>immedi</b>ately" → **warm standby**. "<b>Ze</b>ro <b>down</b>time" → **active/active**. "<b>Ens</b>ure DR <b>enviro</b>nment <b>h</b>as <b>eno</b>ugh <b>capa</b>city <b>lim</b>its" → **raise quotas / ARC readiness check**.

---

## Gotchas and exam traps

- **Launch configurations are deprecated** → <b>lau</b>nch <b>templ</b>ates.
- ASG <b>repl</b>aces <b>insta</b>nces <b>o</b>n <b>fai</b>led **ELB** <b>hea</b>lth <b>che</b>cks <b>on</b>ly <b>i</b>f <b>t</b>he ASG <b>hea</b>lth <b>che</b>ck <b>ty</b>pe <b>incl</b>udes ELB.
- <b>Tar</b>get <b>trac</b>king <b>crea</b>tes <b>i</b>ts <b>o</b>wn <b>ala</b>rms: **don't edit them**.
- **Lifecycle hooks** <b>defa</b>ult 1-<b>ho</b>ur <b>time</b>out; <b>comp</b>lete <b>th</b>em <b>o</b>r <b>th</b>ey <b>ti</b>me <b>o</b>ut (<b>defa</b>ult <b>act</b>ion CONTINUE/ABANDON <b>a</b>s <b>s</b>et).
- ALB **fails open** <b>wh</b>en <b>a</b>ll <b>targ</b>ets <b>a</b>re <b>unhea</b>lthy; 503 <b>wh</b>en <b>no</b>ne <b>regis</b>tered.
- <b>Unhea</b>lthy <b>targ</b>ets → <b>usua</b>lly **SG not allowing the LB** <b>o</b>r <b>wro</b>ng <b>hea</b>lth <b>che</b>ck <b>pa</b>th/<b>co</b>de.
- <b>Rou</b>te 53 <b>hea</b>lth <b>chec</b>kers <b>ne</b>ed **firewall access**; <b>priv</b>ate <b>endpo</b>ints <b>ne</b>ed **CloudWatch alarm** <b>hea</b>lth <b>che</b>cks; DNS TTL <b>del</b>ays <b>fail</b>over.
- **RDS PITR and snapshot restores create a new instance** (<b>n</b>ew <b>endp</b>oint, <b>defa</b>ult SG/<b>param</b>eter <b>gro</b>up <b>unl</b>ess <b>s</b>et).
- **DynamoDB restores create a new table**; <b>reconf</b>igure <b>au</b>to <b>scal</b>ing, <b>stre</b>ams, TTL, <b>ta</b>gs.
- **Aurora Backtrack** = <b>i</b>n <b>pla</b>ce, <b>Aur</b>ora <b>MyS</b>QL <b>on</b>ly.
- **Replication isn't backup** (<b>dele</b>tes/<b>corru</b>ption <b>repli</b>cate).
- S3 <b>versi</b>oning <b>c</b>an <b>on</b>ly <b>b</b>e **suspended**, <b>n</b>ot <b>disa</b>bled; <b>del</b>ete <b>mark</b>ers <b>hi</b>de <b>obje</b>cts.
- **GSI throttling** <b>throt</b>tles <b>ba</b>se-<b>tab</b>le <b>wri</b>tes.
- **ElastiCache Evictions** = <b>cac</b>he <b>t</b>oo <b>sma</b>ll; <b>Red</b>is/<b>Val</b>key <b>wat</b>ch **EngineCPUUtilization**.
- RDS <b>stor</b>age <b>c</b>an <b>gr</b>ow <b>b</b>ut **never shrink**.

## Quick-recall summary

- **Scale**: ASG (<b>tar</b>get <b>trac</b>king, <b>st</b>ep, <b>sched</b>uled, <b>predi</b>ctive, <b>que</b>ue <b>back</b>log; <b>lifec</b>ycle <b>hoo</b>ks, <b>wa</b>rm <b>poo</b>ls, <b>inst</b>ance <b>refr</b>esh), ECS/EKS/<b>Lam</b>bda <b>scal</b>ing, <b>Cloud</b>Front <b>a</b>nd <b>Elasti</b>Cache <b>cach</b>ing, RDS (<b>cla</b>ss, <b>stor</b>age <b>autosc</b>aling, <b>re</b>ad <b>repl</b>icas, <b>Pro</b>xy), <b>Aur</b>ora (<b>repl</b>ica <b>au</b>to <b>scal</b>ing, <b>Serve</b>rless v2 ACUs, <b>au</b>to-<b>pau</b>se), <b>Dyna</b>moDB (<b>o</b>n-<b>dem</b>and <b>v</b>s <b>provis</b>ioned + <b>au</b>to <b>scal</b>ing, DAX, <b>h</b>ot <b>ke</b>ys).
- **Stay up**: ELB <b>hea</b>lth <b>che</b>cks (<b>sett</b>ings, <b>sta</b>tes, <b>troubles</b>hooting), <b>Rou</b>te 53 <b>hea</b>lth <b>che</b>cks (<b>endp</b>oint, <b>calcu</b>lated, <b>ala</b>rm-<b>bas</b>ed; <b>fail</b>over), ARC (<b>readi</b>ness, <b>rout</b>ing <b>cont</b>rol, <b>zon</b>al <b>shi</b>ft), <b>Mul</b>ti-AZ <b>f</b>or <b>eve</b>ry <b>ti</b>er, FIS <b>test</b>ing.
- **Recover**: AWS <b>Bac</b>kup (<b>pla</b>ns, <b>vau</b>lts, <b>Vau</b>lt <b>Lo</b>ck, <b>cro</b>ss-<b>Reg</b>ion/<b>acco</b>unt <b>co</b>py, <b>aud</b>it, <b>rest</b>ore <b>test</b>ing), DLM, <b>Recy</b>cle <b>B</b>in, RDS/<b>Aur</b>ora/<b>Dyna</b>moDB PITR <b>a</b>nd <b>snaps</b>hots, <b>Backt</b>rack, <b>clo</b>nes, S3 <b>a</b>nd <b>F</b>Sx <b>versi</b>oning, DR <b>strat</b>egies <b>wi</b>th RPO/RTO <b>a</b>nd <b>tes</b>ted <b>runb</b>ooks.
