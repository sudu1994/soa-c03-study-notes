# SOA-C03 Cheat Sheet

> 🔰 <b>N</b>ew <b>t</b>o <b>a</b> <b>te</b>rm? <b>Conc</b>epts <b>li</b>nk <b>t</b>o <b>pla</b>in-<b>Engl</b>ish <b>explan</b>ations <b>i</b>n [<b>t</b>he <b>bas</b>ics <b>pa</b>ge](00-cloudops-basics.md) <b>o</b>n <b>fir</b>st <b>ment</b>ion, <b>a</b>nd <b>eve</b>ry <b>abbrev</b>iation <b>i</b>s <b>spel</b>led <b>o</b>ut <b>o</b>n <b>fir</b>st <b>u</b>se <b>a</b>nd <b>lis</b>ted <b>i</b>n <b>t</b>he [<b>glos</b>sary](00-abbreviations-glossary.md).

> <b>Rap</b>id <b>rev</b>iew: "<b>i</b>f <b>t</b>he <b>scen</b>ario <b>sa</b>ys <b>X</b> → <b>d</b>o <b>Y</b>," <b>t</b>op <b>tra</b>ps, <b>a</b>nd <b>numb</b>ers. 65 <b>quest</b>ions (50 <b>sco</b>red), 130 <b>minu</b>tes, <b>pa</b>ss 720/1000. <b>Doma</b>ins: <b>Monit</b>oring/<b>Remedi</b>ation/<b>Perfor</b>mance 22%, <b>Reliab</b>ility 22%, <b>Deplo</b>yment/<b>Autom</b>ation 22%, <b>Secu</b>rity 16%, <b>Netwo</b>rking 18%.

## Keyword → answer decoder

### Monitoring and remediation

| <b>I</b>f <b>t</b>he <b>scen</b>ario <b>sa</b>ys… | <b>D</b>o… |
|---|---|
| <b>Moni</b>tor <b>Elas</b>tic <b>Comp</b>ute <b>Clo</b>ud (EC2) <b>mem</b>ory / <b>di</b>sk <b>spa</b>ce | **CloudWatch agent** (<b>con</b>fig <b>i</b>n <b>Param</b>eter <b>Sto</b>re, <b>inst</b>all <b>v</b>ia <b>R</b>un <b>Comm</b>and/<b>Sta</b>te <b>Mana</b>ger) |
| 1-<b>min</b>ute EC2 <b>metr</b>ics | **Detailed monitoring** |
| <b>S</b>ub-<b>min</b>ute <b>cus</b>tom [<b>met</b>ric](00-cloudops-basics.md#metric) | **[High-resolution](00-cloudops-basics.md#period-and-resolution)** <b>met</b>ric (1 <b>s</b>) |
| <b>Lam</b>bda <b>busi</b>ness <b>metr</b>ics <b>chea</b>ply | **Embedded Metric Format** |
| <b>Cou</b>nt ERROR <b>lin</b>es <b>a</b>nd <b>ale</b>rt | **Metric filter → [alarm](00-cloudops-basics.md#alarm) → Simple Notification Service (SNS)** |
| <b>Str</b>eam <b>lo</b>gs <b>t</b>o S3/<b>OpenS</b>earch <b>contin</b>uously | **Subscription filter → Firehose** |
| <b>Que</b>ry <b>lo</b>gs <b>a</b>d <b>h</b>oc | **Logs Insights** |
| <b>Ala</b>rm <b>on</b>ly <b>wh</b>en <b>t</b>wo <b>condi</b>tions <b>bo</b>th <b>tr</b>ue | **Composite alarm** |
| <b>Reb</b>oot <b>o</b>n <b>inst</b>ance <b>sta</b>tus <b>che</b>ck <b>fail</b>ure | <b>Ala</b>rm `StatusCheckFailed_Instance` → **reboot** |
| <b>Ho</b>st <b>hard</b>ware <b>fail</b>ure | `StatusCheckFailed_System` → **recover** (<b>o</b>r <b>autom</b>atic <b>reco</b>very) |
| <b>Ala</b>rm <b>nev</b>er <b>noti</b>fies, <b>top</b>ic <b>encry</b>pted | <b>U</b>se **customer managed Key Management Service (KMS) key** <b>allo</b>wing <b>Cloud</b>Watch |
| <b>Ala</b>rm <b>stu</b>ck INSUFFICIENT_DATA | <b>Met</b>ric <b>n</b>ot <b>arri</b>ving / <b>wro</b>ng <b>dimen</b>sion / <b>per</b>iod |
| <b>O</b>ne <b>dashb</b>oard <b>f</b>or <b>ma</b>ny <b>acco</b>unts <b>a</b>nd <b>Regi</b>ons | **Cross-account observability** + <b>cro</b>ss-<b>Reg</b>ion <b>dashb</b>oard |
| <b>Sha</b>re <b>dashb</b>oard <b>wi</b>th <b>n</b>on-AWS <b>use</b>rs | **Dashboard sharing** |
| <b>Not</b>ify <b>o</b>n <b>Ama</b>zon <b>W</b>eb <b>Serv</b>ices (AWS) <b>sched</b>uled <b>mainte</b>nance | **EventBridge (aws.health) → SNS** |
| <b>Relat</b>ional <b>Data</b>base <b>Serv</b>ice (RDS) <b>fail</b>over <b>notifi</b>cation | **RDS event subscription → SNS** |
| <b>Rea</b>ct <b>t</b>o <b>a</b>n <b>Applic</b>ation <b>Progra</b>mming <b>Inter</b>face (API) <b>ca</b>ll (<b>e</b>.g., <b>Secu</b>rity <b>Gro</b>up [SG] <b>cha</b>nge) | **EventBridge rule on CloudTrail event** → <b>Lam</b>bda/SSM |
| <b>EventB</b>ridge <b>ru</b>le <b>matc</b>hes <b>noth</b>ing | <b>Ca</b>se-<b>sensi</b>tive <b>patt</b>ern, <b>wro</b>ng <b>Reg</b>ion/<b>b</b>us, <b>Cloud</b>Trail <b>n</b>ot <b>o</b>n, <b>Sim</b>ple <b>Stor</b>age <b>Serv</b>ice (S3) <b>EventB</b>ridge <b>o</b>ff |
| <b>Ru</b>le <b>fir</b>es <b>b</b>ut <b>Lam</b>bda <b>n</b>ot <b>invo</b>ked | <b>Miss</b>ing **Lambda resource policy** |
| <b>R</b>un <b>a</b> <b>f</b>ix <b>acr</b>oss 1,000 <b>insta</b>nces <b>saf</b>ely | **Systems Manager (SSM) Automation/Run Command rate control** |
| <b>Patc</b>hed <b>gol</b>den <b>Ama</b>zon <b>Mach</b>ine <b>Ima</b>ge (AMI) <b>o</b>n <b>sche</b>dule | **Image Builder** (<b>o</b>r `AWS-UpdateLinuxAmi`) |
| <b>Unreac</b>hable <b>inst</b>ance, <b>lo</b>st <b>k</b>ey | **`AWSSupport-ResetAccess` / EC2Rescue** |
| <b>Whi</b>ch <b>Struc</b>tured <b>Que</b>ry <b>Lang</b>uage (SQL) <b>i</b>s <b>sl</b>ow <b>o</b>n RDS | **Performance Insights / Database Insights** |
| OS-<b>lev</b>el RDS <b>metr</b>ics | **Enhanced Monitoring** |
| <b>T</b>oo <b>ma</b>ny DB <b>connec</b>tions <b>fr</b>om <b>Lam</b>bda | **RDS Proxy** |
| <b>Rig</b>ht-<b>si</b>ze EC2/EBS/<b>Lam</b>bda | **Compute Optimizer** |
| gp2 <b>sl</b>ow <b>aft</b>er <b>a</b> <b>whi</b>le ([<b>BurstB</b>alance](00-cloudops-basics.md#burst-credits) 0) | **gp3 with provisioned [Input/Output Operations Per Second [IOPS]](00-cloudops-basics.md#iops-throughput-and-latency)** |
| <b>Cha</b>nge <b>Elas</b>tic <b>Blo</b>ck <b>Sto</b>re (EBS) <b>ty</b>pe <b>with</b>out <b>down</b>time | **Elastic Volumes** |
| <b>Sl</b>ow <b>fir</b>st <b>rea</b>ds <b>aft</b>er <b>rest</b>ore | **Fast Snapshot Restore** / <b>initi</b>alize |
| <b>Glo</b>bal <b>lar</b>ge <b>uplo</b>ads <b>t</b>o S3 | **Transfer Acceleration + multipart** |
| <b>Migr</b>ate <b>Netw</b>ork <b>Fi</b>le <b>Sys</b>tem (NFS) <b>da</b>ta <b>t</b>o S3/EFS <b>wi</b>th <b>verifi</b>cation | **DataSync** |
| <b>Elas</b>tic <b>Fi</b>le <b>Sys</b>tem (EFS) <b>cos</b>ts <b>hi</b>gh <b>f</b>or <b>co</b>ld <b>fil</b>es | **EFS lifecycle → Infrequent Access (IA)/Archive** |
| <b>Spi</b>ky EFS <b>throu</b>ghput <b>throt</b>tled | **Elastic throughput** |
| <b>Fi</b>le <b>acc</b>ess <b>t</b>o <b>exis</b>ting S3 <b>da</b>ta | **Amazon S3 Files** |
| <b>Wind</b>ows <b>fi</b>le <b>sha</b>res <b>wi</b>th <b>Act</b>ive <b>Direc</b>tory (AD) | **FSx for Windows** |
| <b>Low</b>est-<b>late</b>ncy <b>Hi</b>gh-<b>Perfor</b>mance <b>Compu</b>ting (HPC) <b>nod</b>es | **Cluster placement group + Elastic Network Adapter (ENA)/Elastic Fabric Adapter (EFA)** |
| AI-<b>assi</b>sted [<b>inci</b>dent](00-cloudops-basics.md#incident-mttd-and-mttr) <b>investi</b>gation | **AWS DevOps Agent** |
| [<b>Bedr</b>ock](00-cloudops-basics.md#generative-ai-and-foundation-models) <b>usa</b>ge/<b>late</b>ncy/<b>thro</b>ttle <b>monit</b>oring | <b>Bedr</b>ock **CloudWatch metrics + model invocation logging** |
| <b>Kuber</b>netes <b>metr</b>ics <b>wi</b>th <b>Prome</b>theus <b>Que</b>ry <b>Lang</b>uage (<b>Pro</b>mQL) | **Managed Service for Prometheus** (+ <b>Mana</b>ged <b>Graf</b>ana) |

### Reliability and backup

| <b>I</b>f <b>t</b>he <b>scen</b>ario <b>sa</b>ys… | <b>D</b>o… |
|---|---|
| <b>Ke</b>ep <b>Cent</b>ral <b>Proce</b>ssing <b>Un</b>it (CPU) <b>a</b>t 50% | **Target tracking** |
| <b>Kno</b>wn <b>wee</b>kly <b>pea</b>ks | **Scheduled / predictive scaling** |
| <b>Sl</b>ow-<b>boot</b>ing <b>insta</b>nces | **Warm pool** |
| <b>R</b>un <b>scr</b>ipt <b>bef</b>ore <b>termin</b>ation | **Lifecycle hook** |
| <b>Ro</b>ll <b>o</b>ut <b>n</b>ew AMI <b>t</b>o <b>Au</b>to <b>Scal</b>ing <b>gro</b>up (ASG) <b>gradu</b>ally | **Instance refresh** (+ <b>au</b>to <b>roll</b>back) |
| <b>Insta</b>nces <b>repl</b>aced <b>i</b>n <b>a</b> <b>lo</b>op <b>aft</b>er <b>lau</b>nch | <b>Rai</b>se **[health check](00-cloudops-basics.md#health-check) grace period** / <b>f</b>ix <b>hea</b>lth <b>che</b>ck |
| <b>Targ</b>ets <b>unhea</b>lthy <b>b</b>ut <b>a</b>pp <b>wor</b>ks | <b>Tar</b>get **SG must allow LB**; <b>hea</b>lth <b>che</b>ck <b>pa</b>th/<b>cod</b>es |
| <b>Cac</b>he <b>fu</b>ll, <b>hi</b>gh <b>Evict</b>ions | <b>Sca</b>le <b>Elasti</b>Cache <b>u</b>p/<b>o</b>ut |
| RDS <b>stor</b>age <b>nea</b>rly <b>fu</b>ll | **Storage autoscaling** |
| <b>Aur</b>ora <b>re</b>ad <b>lo</b>ad <b>var</b>ies | **Aurora Auto Scaling replicas / Serverless v2** |
| <b>Dyna</b>moDB <b>spi</b>ky [<b>throt</b>tling](00-cloudops-basics.md#throttling) | **On-demand** (<b>o</b>r <b>au</b>to <b>scal</b>ing); <b>che</b>ck **hot keys/Global Secondary Index (GSI)** |
| <b>Hea</b>lth <b>che</b>ck <b>priv</b>ate <b>reso</b>urce <b>i</b>n <b>Rou</b>te 53 | **CloudWatch alarm-based health check** |
| <b>Rou</b>te 53 <b>che</b>cks <b>fa</b>il <b>b</b>ut <b>si</b>te <b>wor</b>ks | <b>All</b>ow **Route 53 health checker IP addresses (IPs)** |
| <b>Shi</b>ft <b>traf</b>fic <b>aw</b>ay <b>fr</b>om <b>impa</b>ired <b>Availa</b>bility <b>Zo</b>ne (AZ) | **Application Recovery Controller (ARC) zonal shift / autoshift** |
| DB <b>surv</b>ives AZ <b>fail</b>ure <b>automat</b>ically | **RDS [Multi-AZ](00-cloudops-basics.md#multi-az)** |
| <b>Netw</b>ork <b>Addr</b>ess <b>Transl</b>ation (NAT) <b>out</b>age <b>wh</b>en <b>o</b>ne AZ <b>fai</b>ls | **NAT gateway per AZ** |
| <b>Centra</b>lized <b>back</b>ups <b>acr</b>oss <b>serv</b>ices/<b>acco</b>unts | **AWS Backup** + <b>o</b>rg <b>bac</b>kup <b>poli</b>cies |
| [<b>Immut</b>able](00-cloudops-basics.md#immutable-vs-mutable-infrastructure) <b>back</b>ups | **Backup Vault Lock ([compliance](00-cloudops-basics.md#compliance-as-code--continuous-compliance))** |
| <b>Pro</b>ve <b>bac</b>kup <b>compl</b>iance | **Backup Audit Manager** |
| <b>Autom</b>ated EBS [<b>snaps</b>hots](00-cloudops-basics.md#backup-vs-snapshot-vs-replication) <b>b</b>y <b>t</b>ag | **Data Lifecycle Manager** |
| <b>Reco</b>ver <b>dele</b>ted <b>snap</b>shot/AMI | **Recycle Bin** |
| <b>Un</b>do <b>b</b>ad <b>Aur</b>ora <b>MyS</b>QL <b>cha</b>nge <b>i</b>n <b>pla</b>ce | **Backtrack** |
| <b>Rest</b>ore <b>t</b>o <b>bef</b>ore <b>accid</b>ental <b>del</b>ete | **[Point-In-Time Recovery [PITR]](00-cloudops-basics.md#point-in-time-recovery-pitr)** (<b>crea</b>tes <b>n</b>ew <b>inst</b>ance/<b>tab</b>le) |
| <b>Use</b>rs <b>reco</b>ver <b>overwr</b>itten S3 <b>obje</b>cts | **[Versioning](00-cloudops-basics.md#versioning)** |
| <b>Versi</b>oned <b>buc</b>ket <b>grow</b>ing | **Noncurrent version expiration** |
| <b>Wind</b>ows <b>use</b>rs <b>rest</b>ore <b>prev</b>ious <b>fi</b>le <b>vers</b>ions | **FSx shadow copies** |
| <b>Disa</b>ster <b>Reco</b>very (DR) <b>chea</b>pest / <b>minu</b>tes <b>Reco</b>very <b>Poi</b>nt <b>Objec</b>tive (RPO) / <b>minu</b>tes [<b>Reco</b>very <b>Ti</b>me <b>Objec</b>tive [RTO]](00-cloudops-basics.md#rpo-and-rto) / <b>ze</b>ro | <b>Bac</b>kup & <b>rest</b>ore / **pilot light** / **[warm standby](00-cloudops-basics.md#disaster-recovery-strategies)** / **active-active** |

### Deployment and automation

| <b>I</b>f <b>t</b>he <b>scen</b>ario <b>sa</b>ys… | <b>D</b>o… |
|---|---|
| <b>Prev</b>iew <b>whet</b>her <b>upd</b>ate <b>repl</b>aces <b>reso</b>urce | **Change set** |
| <b>Ke</b>ep DB <b>wh</b>en <b>sta</b>ck <b>dele</b>ted | **DeletionPolicy Retain/Snapshot** |
| <b>Prev</b>ent <b>upda</b>tes <b>t</b>o <b>crit</b>ical <b>reso</b>urce | **Stack policy** |
| <b>Prev</b>ent <b>sta</b>ck <b>dele</b>tion | **Termination protection** |
| <b>Det</b>ect <b>man</b>ual <b>chan</b>ges | **[Drift](00-cloudops-basics.md#drift) detection** |
| <b>Wa</b>it <b>f</b>or <b>boots</b>trap <b>bef</b>ore <b>sta</b>ck <b>compl</b>etes | **CreationPolicy + cfn-signal** |
| <b>Sta</b>ck `UPDATE_ROLLBACK_FAILED` | <b>F</b>ix → **ContinueUpdateRollback** |
| <b>Sta</b>ck `ROLLBACK_COMPLETE` | <b>Del</b>ete <b>a</b>nd <b>recr</b>eate |
| <b>Sta</b>ck <b>stu</b>ck ~1 <b>h</b> | <b>Cus</b>tom <b>reso</b>urce/<b>c</b>fn-<b>sig</b>nal <b>n</b>ot <b>respo</b>nding |
| <b>Requ</b>ires <b>capabi</b>lities <b>err</b>or | **CAPABILITY_IAM / NAMED_IAM / AUTO_EXPAND** |
| <b>Secr</b>ets <b>i</b>n <b>templ</b>ates | **Dynamic references** |
| <b>Fir</b>st <b>Clo</b>ud <b>Develo</b>pment <b>K</b>it (CDK) <b>dep</b>loy <b>fai</b>ls | **`cdk bootstrap`** |
| <b>Dep</b>loy <b>base</b>line <b>t</b>o <b>a</b>ll <b>acco</b>unts <b>in</b>cl. <b>n</b>ew | **Service-managed StackSets + auto-deploy** |
| <b>Sha</b>re <b>subn</b>ets/<b>Tran</b>sit <b>Gate</b>way (TGW)/<b>Reso</b>lver <b>rul</b>es | **AWS RAM** |
| [<b>Terra</b>form](00-cloudops-basics.md#terraform-basics) <b>sta</b>te <b>corru</b>ption <b>fr</b>om <b>concu</b>rrent <b>ru</b>ns | **S3 backend + state locking** |
| <b>Po</b>ds/<b>Lam</b>bda <b>can</b>'t <b>g</b>et IPs | **Subnet exhaustion** → <b>a</b>dd [<b>Class</b>less <b>Int</b>er-<b>Dom</b>ain <b>Rout</b>ing [CIDR]](00-cloudops-basics.md#cidr-and-subnet-sizing)/<b>subn</b>ets |
| <b>Ze</b>ro <b>down</b>time <b>wi</b>th <b>inst</b>ant <b>roll</b>back | **[Blue/green](00-cloudops-basics.md#deployment-strategies)** |
| 10% <b>th</b>en 100% <b>Lam</b>bda <b>traf</b>fic | **CodeDeploy canary** <b>o</b>n <b>ali</b>as |
| <b>Au</b>to-<b>roll</b>back <b>fail</b>ing <b>Elas</b>tic <b>Conta</b>iner <b>Serv</b>ice (ECS) <b>deplo</b>yment | **Deployment circuit breaker** |
| <b>Pat</b>ch <b>fle</b>et <b>o</b>n <b>sche</b>dule | **Patch Manager + Maintenance Window** (<b>o</b>r <b>pat</b>ch <b>pol</b>icy) |
| <b>Ke</b>ep <b>age</b>nt <b>insta</b>lled <b>o</b>n <b>a</b>ll <b>insta</b>nces | **State Manager association** |
| <b>Inst</b>ance <b>n</b>ot <b>mana</b>ged <b>b</b>y SSM | <b>Age</b>nt / **instance profile** / **SSM endpoints** |
| <b>Nigh</b>tly <b>st</b>op <b>o</b>f <b>d</b>ev <b>insta</b>nces | **EventBridge Scheduler → SSM Automation** |
| <b>Proc</b>ess S3 <b>uplo</b>ads <b>insta</b>ntly | **S3 event → Lambda/Simple Queue Service [SQS]** (<b>EventB</b>ridge <b>f</b>or <b>adva</b>nced) |

### Security and compliance

| <b>I</b>f <b>t</b>he <b>scen</b>ario <b>sa</b>ys… | <b>D</b>o… |
|---|---|
| <b>Pass</b>word <b>rota</b>tion <b>a</b>nd <b>reu</b>se <b>rul</b>es | **Identity and Access Management (IAM) password policy** |
| <b>Requ</b>ire [<b>Mul</b>ti-<b>Fac</b>tor <b>Authent</b>ication [MFA]](00-cloudops-basics.md#mfa) | <b>De</b>ny <b>unl</b>ess **`aws:MultiFactorAuthPresent`** |
| <b>Te</b>st <b>a</b> <b>pol</b>icy <b>bef</b>ore <b>attac</b>hing | **IAM policy simulator** |
| <b>Fi</b>nd <b>resou</b>rces <b>sha</b>red <b>exter</b>nally | **IAM Access Analyzer** |
| <b>Lea</b>st-<b>privi</b>lege <b>pol</b>icy <b>fr</b>om <b>usa</b>ge | **Access Analyzer policy generation** |
| <b>Enco</b>ded <b>authori</b>zation <b>fail</b>ure | **`sts decode-authorization-message`** |
| <b>Aud</b>it <b>k</b>ey <b>a</b>ge/MFA <b>f</b>or <b>a</b>ll <b>use</b>rs | **Credential report** |
| <b>Blo</b>ck <b>unapp</b>roved <b>Regi</b>ons | **Service Control Policy (SCP) with `aws:RequestedRegion`** |
| <b>Workf</b>orce <b>Sin</b>gle <b>Si</b>gn-<b>O</b>n (SSO) <b>wi</b>th <b>corpo</b>rate <b>Iden</b>tity <b>Prov</b>ider (<b>I</b>dP) | **IAM Identity Center** (+ <b>Sys</b>tem <b>f</b>or <b>Cro</b>ss-<b>dom</b>ain <b>Iden</b>tity <b>Manag</b>ement [SCIM]) |
| <b>Trus</b>ted <b>Advi</b>sor <b>fla</b>gs <b>op</b>en <b>Sec</b>ure <b>She</b>ll (SSH) | <b>Rest</b>rict SG; <b>Con</b>fig <b>ru</b>le + [<b>remedi</b>ation](00-cloudops-basics.md#remediation) |
| <b>Auto</b>mate <b>o</b>n <b>Trus</b>ted <b>Advi</b>sor <b>resu</b>lts | **EventBridge (us-east-1) → Lambda** |
| <b>O</b>rg-<b>wi</b>de <b>Paym</b>ent <b>Ca</b>rd <b>Indu</b>stry (PCI)/<b>Cen</b>ter <b>f</b>or <b>Inte</b>rnet <b>Secu</b>rity (CIS) <b>ru</b>le <b>bun</b>dle | **Config conformance pack** |
| <b>Au</b>to-<b>f</b>ix <b>n</b>on-<b>compl</b>iant <b>resou</b>rces | **Config remediation (SSM Automation)** |
| <b>Fi</b>nd <b>Perso</b>nally <b>Identi</b>fiable <b>Inform</b>ation (PII) <b>i</b>n S3 | **Macie** |
| <b>Enfo</b>rce [<b>classif</b>ication](00-cloudops-basics.md#data-classification) [<b>ta</b>gs](00-cloudops-basics.md#tags) | **Tag policies + Config required-tags + SCP** |
| <b>A</b>ll <b>n</b>ew EBS <b>encry</b>pted | **EBS encryption by default** |
| <b>Encr</b>ypt <b>exis</b>ting RDS | <b>Snap</b>shot → **copy encrypted** → <b>rest</b>ore |
| <b>Sha</b>re <b>encry</b>pted AMI/<b>snap</b>shot | **Customer managed key** <b>sha</b>red |
| ASG <b>can</b>'t <b>lau</b>nch <b>encry</b>pted <b>volu</b>mes | <b>K</b>ey <b>pol</b>icy <b>mu</b>st <b>all</b>ow **Auto Scaling service-linked role** |
| <b>Au</b>to-<b>rene</b>wing [<b>certif</b>icates](00-cloudops-basics.md#tls-and-certificates) | **AWS Certificate Manager (ACM) + [Domain Name System [DNS]](00-cloudops-basics.md#dns-records-and-ttl) validation** |
| <b>Impo</b>rted <b>ce</b>rt <b>expi</b>ring | **Re-import**; <b>ala</b>rm <b>o</b>n `DaysToExpiry`/<b>EventB</b>ridge |
| <b>Rot</b>ate DB <b>passw</b>ords | **Secrets Manager** |
| <b>Che</b>ap <b>con</b>fig + <b>sec</b>ure <b>stri</b>ngs | **Parameter Store SecureString** |
| <b>Isol</b>ate <b>inst</b>ance <b>o</b>n <b>Guard</b>Duty <b>find</b>ing | **EventBridge → Lambda/SSM quarantine SG** |
| <b>Cent</b>ral <b>find</b>ings + <b>sco</b>re | **Security Hub** (<b>deleg</b>ated <b>adm</b>in, <b>cro</b>ss-<b>Reg</b>ion <b>aggreg</b>ation) |
| <b>Conti</b>nuous <b>Com</b>mon <b>Vulnerab</b>ilities <b>a</b>nd <b>Expos</b>ures (CVE) <b>scan</b>ning | **Inspector** |
| <b>Autom</b>ated <b>p</b>en <b>test</b>ing <b>wi</b>th <b>f</b>ix PRs | **AWS Security Agent** |

### Networking

| <b>I</b>f <b>t</b>he <b>scen</b>ario <b>sa</b>ys… | <b>D</b>o… |
|---|---|
| <b>Priv</b>ate <b>inst</b>ance <b>nee</b>ds <b>inte</b>rnet | **NAT gateway** ([<b>pub</b>lic <b>sub</b>net](00-cloudops-basics.md#public-vs-private-subnet)) + <b>rou</b>te |
| <b>Inte</b>rnet <b>Prot</b>ocol <b>vers</b>ion 6 (IPv6) <b>outb</b>ound <b>on</b>ly | **Egress-only Internet Gateway (IGW)** |
| S3 <b>with</b>out <b>inte</b>rnet/NAT | **Gateway endpoint** |
| SSM/<b>Cloud</b>Watch/KMS <b>with</b>out NAT | **Interface endpoints** (SG 443, <b>priv</b>ate DNS) |
| <b>Prov</b>ide <b>serv</b>ice <b>t</b>o <b>ma</b>ny <b>Virt</b>ual <b>Priv</b>ate <b>Clo</b>uds (VPCs), <b>overla</b>pping <b>Class</b>less <b>Int</b>er-<b>Dom</b>ain <b>Rout</b>ing <b>ran</b>ges (CIDRs) | **PrivateLink endpoint service** (NLB) |
| <b>W</b>hy <b>can</b>'t <b>A</b> <b>rea</b>ch <b>B</b>? (<b>con</b>fig) | **Reachability Analyzer** |
| <b>Fi</b>nd <b>unint</b>ended <b>inte</b>rnet <b>pat</b>hs | **Network Access Analyzer** |
| <b>Inbo</b>und ACCEPT, <b>outb</b>ound REJECT | **Network Access Control List (NACL) [ephemeral ports](00-cloudops-basics.md#stateful-vs-stateless-filtering)** |
| <b>O</b>n-<b>pr</b>em <b>reso</b>lves AWS <b>priv</b>ate <b>zo</b>ne | **Resolver inbound endpoint** |
| <b>Virt</b>ual <b>Priv</b>ate <b>Clo</b>ud (VPC) <b>reso</b>lves <b>o</b>n-<b>pr</b>em <b>dom</b>ain | **Outbound endpoint + forwarding rule** |
| <b>L</b>og DNS <b>quer</b>ies <b>fr</b>om VPC | **Resolver query logging** |
| <b>Ap</b>ex <b>t</b>o <b>Applic</b>ation <b>Lo</b>ad <b>Bala</b>ncer (ALB) | **Alias** |
| <b>Dom</b>ain <b>does</b>n't <b>reso</b>lve <b>aft</b>er <b>n</b>ew <b>zo</b>ne | <b>F</b>ix **registrar Name Server (NS)** <b>reco</b>rds |
| <b>W</b>eb <b>Applic</b>ation <b>Fire</b>wall (WAF) <b>bloc</b>ked <b>leg</b>it <b>requ</b>est | **WAF logs `terminatingRuleId`**, <b>Cou</b>nt <b>mo</b>de |
| <b>Blo</b>ck <b>malic</b>ious <b>doma</b>ins <b>a</b>t DNS | **DNS Firewall** (<b>mana</b>ged <b>lis</b>ts) |
| ALB <b>v</b>s <b>tar</b>get <b>err</b>ors | **ALB access logs** / `HTTPCode_ELB_5XX` <b>v</b>s `HTTPCode_Target_5XX` |
| <b>L</b>ow <b>Cloud</b>Front <b>h</b>it <b>rat</b>io | <b>Nar</b>row **[cache key](00-cloudops-basics.md#cdn-origin-and-cache-key)** (<b>cac</b>he <b>pol</b>icy), TTLs |
| <b>Sta</b>le <b>Cloud</b>Front <b>cont</b>ent | **Versioned names** <b>o</b>r <b>invali</b>dation |
| <b>Cloud</b>Front [403](00-cloudops-basics.md#http-status-codes) <b>wi</b>th S3 | **Origin Access Control (OAC)** <b>buc</b>ket <b>pol</b>icy / KMS <b>pol</b>icy / <b>defa</b>ult <b>ro</b>ot <b>obj</b>ect |
| <b>Cloud</b>Front 502 | <b>Ori</b>gin **Transport Layer Security (TLS) certificate** <b>iss</b>ue |
| <b>Cloud</b>Front 504 | <b>Ori</b>gin <b>time</b>out / <b>fire</b>wall |
| <b>Virt</b>ual <b>Priv</b>ate <b>Netw</b>ork (VPN) <b>u</b>p, <b>n</b>o <b>traf</b>fic | **Route propagation**/<b>rou</b>tes, SG/NACL |
| VPN <b>dro</b>ps <b>wh</b>en <b>id</b>le | **Dead Peer Detection (DPD)/keepalive, startup action** |
| <b>Cli</b>ent VPN <b>use</b>rs <b>can</b>'t <b>rea</b>ch VPC | **Authorization rules + routes** |
| ISP <b>iss</b>ues <b>affec</b>ting <b>use</b>rs | **Internet Monitor** |
| <b>Pac</b>ket <b>lo</b>ss <b>t</b>o <b>o</b>n-<b>pr</b>em <b>ov</b>er <b>Dir</b>ect <b>Conn</b>ect (DX) | **Network Synthetic Monitor** |
| <b>Retransm</b>issions <b>betw</b>een <b>serv</b>ices <b>i</b>n AWS | **Network Flow Monitor** |
| NAT <b>hi</b>gh <b>bi</b>ll <b>fr</b>om S3 <b>traf</b>fic | **S3 gateway endpoint** |

## Top traps

1. <b>Mem</b>ory/<b>di</b>sk <b>metr</b>ics <b>ne</b>ed <b>t</b>he **agent**.
2. **Metric filters** <b>are</b>n't <b>retroa</b>ctive.
3. <b>Compo</b>site <b>ala</b>rms <b>can</b>'t <b>d</b>o **EC2/Auto Scaling actions**.
4. **Recover** = <b>sys</b>tem <b>che</b>ck; **reboot** = <b>inst</b>ance <b>che</b>ck.
5. <b>Glo</b>bal <b>serv</b>ice <b>eve</b>nts (IAM, <b>Cloud</b>Front) <b>i</b>n **us-east-1**.
6. <b>EventB</b>ridge <b>targ</b>ets <b>ne</b>ed **resource policies or roles**.
7. <b>Lau</b>nch **configurations** <b>a</b>re <b>depre</b>cated.
8. <b>Unhea</b>lthy <b>targ</b>ets → **SG doesn't allow LB**.
9. **PITR/snapshot restores create new resources**.
10. **Replication isn't backup**.
11. `ROLLBACK_COMPLETE` → <b>del</b>ete; `UPDATE_ROLLBACK_FAILED` → <b>cont</b>inue <b>roll</b>back.
12. **CDK bootstrap** <b>p</b>er <b>acco</b>unt/<b>Reg</b>ion.
13. <b>Serv</b>ice <b>Cont</b>rol <b>Poli</b>cies (SCPs) <b>don</b>'t <b>aff</b>ect <b>t</b>he **management account**.
14. **AWS managed keys** <b>can</b>'t <b>b</b>e <b>sha</b>red.
15. **Imported ACM certs** <b>don</b>'t <b>ren</b>ew; <b>Cloud</b>Front <b>cer</b>ts <b>i</b>n **us-east-1**.
16. **Network Access Control Lists (NACLs) stateless**; <b>time</b>out = <b>dr</b>op, <b>refu</b>sed = <b>ho</b>st <b>reac</b>hed.
17. **Gateway endpoints** <b>don</b>'t <b>wo</b>rk <b>fr</b>om <b>o</b>n-<b>pr</b>em.
18. <b>Elas</b>tic <b>Lo</b>ad <b>Balan</b>cing (ELB) <b>acc</b>ess <b>lo</b>gs **off by default**.
19. <b>L</b>ow <b>Cloud</b>Front <b>h</b>it <b>rat</b>io = <b>cac</b>he <b>k</b>ey <b>t</b>oo <b>bro</b>ad.
20. VPN "<b>u</b>p <b>b</b>ut <b>n</b>o <b>traf</b>fic" = **routes**.

## Numbers

| <b>It</b>em | <b>Val</b>ue |
|---|---|
| <b>Cloud</b>Watch <b>reten</b>tion | < 60 <b>s</b>: 3 <b>h</b>; 1 <b>m</b>in: 15 <b>da</b>ys; 5 <b>m</b>in: 63 <b>da</b>ys; 1 <b>h</b>: 455 <b>da</b>ys |
| EC2 <b>bas</b>ic / <b>deta</b>iled <b>monit</b>oring | 5 <b>m</b>in / 1 <b>m</b>in |
| <b>Lo</b>gs <b>subscr</b>iption <b>filt</b>ers | 2 <b>p</b>er <b>l</b>og <b>gro</b>up |
| <b>Lo</b>gs <b>exp</b>ort <b>ta</b>sk | <b>u</b>p <b>t</b>o 12 <b>h</b>, <b>n</b>ot <b>re</b>al <b>ti</b>me |
| <b>Cloud</b>Trail <b>eve</b>nt <b>hist</b>ory | 90 <b>da</b>ys (<b>manag</b>ement <b>eve</b>nts) |
| <b>Cloud</b>Trail S3 <b>deli</b>very | ~5 <b>minu</b>tes |
| <b>EventB</b>ridge <b>retr</b>ies | <b>u</b>p <b>t</b>o 24 <b>h</b> / 185 <b>atte</b>mpts |
| ASG <b>cool</b>down / <b>gra</b>ce <b>per</b>iod | 300 <b>s</b> <b>defa</b>ult |
| <b>Lifec</b>ycle <b>ho</b>ok <b>time</b>out | 1 <b>h</b> <b>defa</b>ult |
| ELB <b>hea</b>lth <b>che</b>ck <b>inte</b>rval / <b>deregis</b>tration <b>del</b>ay | 30 <b>s</b> / 300 <b>s</b> |
| <b>Rou</b>te 53 <b>hea</b>lth <b>che</b>ck <b>inte</b>rval | 30 <b>s</b> <b>stan</b>dard, 10 <b>s</b> <b>fa</b>st; <b>thres</b>hold 3 |
| RDS <b>bac</b>kup <b>reten</b>tion / <b>Mul</b>ti-AZ <b>fail</b>over | 0–35 <b>da</b>ys / 60–120 <b>s</b> (<b>clus</b>ter < 35 <b>s</b>) |
| <b>Aur</b>ora <b>repl</b>icas / <b>fail</b>over | 15 / ~30 <b>s</b>; <b>Backt</b>rack <b>u</b>p <b>t</b>o 72 <b>h</b> |
| <b>Dyna</b>moDB PITR | <b>u</b>p <b>t</b>o 35 <b>da</b>ys |
| <b>Bac</b>kup <b>Vau</b>lt <b>Lo</b>ck <b>compl</b>iance <b>gra</b>ce | ≥ 72 <b>h</b> |
| KMS <b>k</b>ey <b>dele</b>tion <b>wa</b>it | 7–30 <b>da</b>ys; <b>rota</b>tion 90–2,560 <b>da</b>ys (<b>defa</b>ult 365) |
| ACM <b>rene</b>wal <b>sta</b>rts / <b>exp</b>iry <b>eve</b>nts | 60 <b>da</b>ys / <b>fr</b>om 45 <b>da</b>ys <b>bef</b>ore |
| <b>Param</b>eter <b>Sto</b>re | 4 KB <b>stan</b>dard, 8 KB <b>adva</b>nced |
| <b>Secr</b>ets <b>Mana</b>ger <b>sec</b>ret | 64 KB |
| <b>Sub</b>net <b>rese</b>rved IPs | 5 |
| SG <b>rul</b>es / <b>Secu</b>rity <b>Gro</b>ups (SGs) <b>p</b>er <b>Elas</b>tic <b>Netw</b>ork <b>Inter</b>face (ENI) | 60 <b>i</b>n + 60 <b>o</b>ut / 5 |
| NAT <b>gate</b>way | 5–100 <b>Gb</b>ps, 55,000 <b>connec</b>tions <b>p</b>er <b>destin</b>ation <b>p</b>er <b>Inte</b>rnet <b>Prot</b>ocol (IP), 350 <b>s</b> <b>id</b>le <b>time</b>out |
| VPN | 2 <b>tunn</b>els, ~1.25 <b>Gb</b>ps <b>ea</b>ch |
| <b>Cloud</b>Front <b>ori</b>gin <b>resp</b>onse <b>time</b>out | 30 <b>s</b> <b>defa</b>ult; <b>err</b>or [<b>cach</b>ing](00-cloudops-basics.md#caching) 10 <b>s</b> <b>defa</b>ult |
| S3 <b>p</b>er <b>pre</b>fix | 3,500 <b>wri</b>tes / 5,500 <b>rea</b>ds <b>p</b>er <b>sec</b>ond |
| gp3 <b>base</b>line | 3,000 IOPS / 125 <b>M</b>iB/<b>s</b> |
| <b>Spr</b>ead <b>place</b>ment <b>gro</b>up | 7 <b>insta</b>nces <b>p</b>er AZ |
