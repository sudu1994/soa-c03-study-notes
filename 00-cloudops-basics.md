# CloudOps Basics (the 101 layer)

> <b>Pla</b>in-<b>Engl</b>ish <b>explan</b>ations <b>o</b>f <b>t</b>he <b>opera</b>tions, <b>monit</b>oring, <b>autom</b>ation, <b>netwo</b>rking <b>a</b>nd <b>secu</b>rity <b>ide</b>as <b>t</b>he SOA-C03 <b>not</b>es <b>re</b>ly <b>o</b>n. <b>Clo</b>ud <b>Practi</b>tioner <b>bas</b>ics <b>a</b>re <b>le</b>ft <b>o</b>ut. <b>I</b>n <b>t</b>he <b>dom</b>ain <b>fil</b>es, <b>t</b>he <b>fir</b>st <b>ment</b>ion <b>o</b>f <b>ea</b>ch <b>conc</b>ept <b>lin</b>ks <b>ba</b>ck <b>he</b>re. <b>Abbrevi</b>ations <b>a</b>re <b>i</b>n [00-abbreviations-glossary.md](00-abbreviations-glossary.md).

## How SOA questions differ from SAA

SAA <b>as</b>ks *<b>whi</b>ch <b>des</b>ign*. <b>Sta</b>rt <b>o</b>f <b>Autho</b>rity (SOA) <b>as</b>ks *<b>h</b>ow <b>d</b>o <b>y</b>ou <b>oper</b>ate, <b>f</b>ix, <b>auto</b>mate <b>a</b>nd <b>pro</b>ve <b>i</b>t*: <b>whi</b>ch <b>met</b>ric <b>sho</b>ws <b>t</b>he <b>prob</b>lem, <b>whi</b>ch <b>sett</b>ing <b>t</b>o <b>cha</b>nge, <b>whi</b>ch <b>l</b>og <b>tel</b>ls <b>y</b>ou <b>w</b>hy, <b>whi</b>ch <b>autom</b>ation <b>fix</b>es <b>i</b>t <b>with</b>out <b>a</b> <b>hum</b>an. <b>Exp</b>ect "<b>t</b>he <b>ala</b>rm <b>does</b>n't <b>fi</b>re," "<b>t</b>he <b>sta</b>ck <b>i</b>s <b>stu</b>ck," "<b>t</b>he <b>inst</b>ance <b>can</b>'t <b>rea</b>ch <b>t</b>he <b>inte</b>rnet," "<b>use</b>rs <b>g</b>et 403 <b>fr</b>om <b>Cloud</b>Front" <b>sty</b>le <b>quest</b>ions.

| <b>Const</b>raint <b>wo</b>rd | <b>Usua</b>lly <b>mea</b>ns |
|---|---|
| "<b>lea</b>st <b>operat</b>ional <b>over</b>head" / "<b>auto</b>mate" | <b>Mana</b>ged <b>feat</b>ure <b>o</b>r <b>Syst</b>ems <b>Mana</b>ger/<b>EventB</b>ridge <b>autom</b>ation <b>ov</b>er <b>cus</b>tom <b>scri</b>pts |
| "<b>mo</b>st <b>co</b>st-<b>effec</b>tive" | <b>Chea</b>pest <b>opt</b>ion <b>th</b>at <b>sti</b>ll <b>mee</b>ts *<b>a</b>ll* <b>requir</b>ements |
| "<b>troubl</b>eshoot" / "<b>iden</b>tify <b>t</b>he <b>cau</b>se" | <b>T</b>he <b>spec</b>ific <b>l</b>og, <b>met</b>ric <b>o</b>r <b>to</b>ol <b>th</b>at <b>reve</b>als <b>i</b>t (<b>Fl</b>ow <b>Lo</b>gs, <b>Reacha</b>bility <b>Anal</b>yzer, <b>Cloud</b>Trail, <b>pol</b>icy <b>simul</b>ator) |
| "<b>acr</b>oss <b>a</b>ll <b>acco</b>unts/<b>Regi</b>ons" | <b>Organiz</b>ations + <b>Stack</b>Sets / <b>deleg</b>ated <b>adm</b>in / <b>aggreg</b>ators / <b>cro</b>ss-<b>acco</b>unt <b>observa</b>bility |
| "<b>ne</b>ar <b>re</b>al <b>ti</b>me" | <b>EventB</b>ridge, <b>Cloud</b>Watch <b>Lo</b>gs <b>subscr</b>iption, <b>met</b>ric <b>filt</b>ers (<b>n</b>ot <b>Cloud</b>Trail <b>Sim</b>ple <b>Stor</b>age <b>Serv</b>ice [S3] <b>deli</b>very, <b>whi</b>ch <b>la</b>gs) |

---

## Monitoring concepts

### Metric
<b>A</b> <b>num</b>ber <b>meas</b>ured <b>ov</b>er <b>ti</b>me, <b>e</b>.g., `CPUUtilization` <b>eve</b>ry <b>min</b>ute. <b>I</b>n <b>Cloud</b>Watch <b>a</b> <b>met</b>ric <b>liv</b>es <b>i</b>n <b>a</b> **namespace** (`AWS/EC2`) <b>a</b>nd <b>i</b>s <b>ident</b>ified <b>b</b>y **dimensions** (`InstanceId=i-123`).

### Statistics and percentiles
<b>H</b>ow <b>da</b>ta <b>poi</b>nts <b>i</b>n <b>a</b> <b>per</b>iod <b>a</b>re <b>summa</b>rized: **Average**, **Sum**, **Minimum**, **Maximum**, **SampleCount**, <b>a</b>nd **percentiles** <b>li</b>ke **p99** ("99% <b>o</b>f <b>requ</b>ests <b>we</b>re <b>fas</b>ter <b>th</b>an <b>th</b>is"). <b>U</b>se p99/p95 <b>f</b>or <b>late</b>ncy (<b>aver</b>ages <b>hi</b>de <b>sl</b>ow <b>outl</b>iers), <b>S</b>um <b>f</b>or <b>cou</b>nts (<b>err</b>ors, <b>requ</b>ests), <b>Maxi</b>mum <b>f</b>or <b>spi</b>kes.

### Period and resolution
**Period** = <b>t</b>he <b>ti</b>me <b>buc</b>ket <b>a</b> <b>stati</b>stic <b>cov</b>ers (60 <b>s</b>, 300 <b>s</b>). **Standard resolution** = 1-<b>min</b>ute <b>granul</b>arity; **high resolution** = <b>do</b>wn <b>t</b>o 1-<b>sec</b>ond (<b>cus</b>tom <b>metr</b>ics <b>on</b>ly).

### Alarm
<b>A</b> <b>ru</b>le <b>watc</b>hing <b>o</b>ne <b>met</b>ric (<b>o</b>r <b>a</b> <b>ma</b>th <b>expre</b>ssion) <b>th</b>at <b>chan</b>ges <b>sta</b>te <b>wh</b>en <b>a</b> <b>thres</b>hold <b>i</b>s <b>cros</b>sed <b>f</b>or <b>a</b> <b>num</b>ber <b>o</b>f <b>peri</b>ods, <b>a</b>nd <b>th</b>en <b>tak</b>es **actions** (<b>not</b>ify, <b>sca</b>le, <b>st</b>op/<b>reb</b>oot/<b>reco</b>ver <b>a</b>n <b>inst</b>ance, <b>r</b>un <b>autom</b>ation).

### Logs, log aggregation and structured logs
**Logs** <b>a</b>re <b>timest</b>amped <b>eve</b>nt <b>reco</b>rds. **Aggregation** <b>sen</b>ds <b>th</b>em <b>fr</b>om <b>ma</b>ny <b>sour</b>ces <b>t</b>o <b>o</b>ne <b>pla</b>ce (<b>Cloud</b>Watch <b>Lo</b>gs) <b>t</b>o <b>sea</b>rch <b>a</b>nd <b>ale</b>rt. **Structured logs** (<b>JavaS</b>cript <b>Obj</b>ect <b>Nota</b>tion [JSON] <b>fie</b>lds) <b>a</b>re <b>mu</b>ch <b>eas</b>ier <b>t</b>o <b>que</b>ry <b>a</b>nd <b>t</b>o <b>tu</b>rn <b>in</b>to <b>metr</b>ics <b>th</b>an <b>fr</b>ee <b>te</b>xt.

### Traces
<b>A</b> <b>rec</b>ord <b>o</b>f <b>o</b>ne <b>reque</b>st's <b>pa</b>th <b>thro</b>ugh <b>ma</b>ny <b>serv</b>ices <b>wi</b>th <b>tim</b>ing <b>f</b>or <b>ea</b>ch <b>h</b>op. <b>Us</b>ed <b>t</b>o <b>fi</b>nd <b>whi</b>ch <b>compo</b>nent <b>i</b>s <b>sl</b>ow <b>o</b>r <b>fail</b>ing (<b>X</b>-<b>R</b>ay).

### Dashboards
<b>A</b> <b>pa</b>ge <b>o</b>f <b>gra</b>phs <b>a</b>nd <b>numb</b>ers <b>f</b>or <b>a</b>t-<b>a</b>-<b>gla</b>nce <b>hea</b>lth. <b>Go</b>od <b>dashb</b>oards <b>sh</b>ow <b>t</b>he <b>us</b>er-<b>fac</b>ing <b>sign</b>als <b>fir</b>st (<b>err</b>ors, <b>late</b>ncy, <b>traf</b>fic, <b>satur</b>ation: <b>t</b>he "<b>gol</b>den <b>sign</b>als").

### Baseline and anomaly
<b>A</b> **baseline** <b>i</b>s <b>nor</b>mal <b>beha</b>vior <b>f</b>or <b>a</b> <b>met</b>ric (<b>oft</b>en <b>wi</b>th <b>dai</b>ly/<b>wee</b>kly <b>patt</b>erns). <b>A</b>n **anomaly** <b>i</b>s <b>a</b> <b>devia</b>tion <b>fr</b>om <b>i</b>t. <b>Anom</b>aly <b>detec</b>tion <b>ala</b>rms <b>u</b>se <b>a</b> <b>lear</b>ned <b>ba</b>nd <b>inst</b>ead <b>o</b>f <b>a</b> <b>fix</b>ed <b>thres</b>hold.

### Synthetic vs real-user monitoring
**Synthetic** <b>monit</b>oring <b>ru</b>ns <b>scri</b>pted <b>che</b>cks <b>o</b>n <b>a</b> <b>sche</b>dule (<b>a</b> <b>fa</b>ke <b>us</b>er <b>lo</b>gs <b>i</b>n <b>eve</b>ry 5 <b>minu</b>tes). **Real-user monitoring (RUM)** <b>meas</b>ures <b>wh</b>at <b>act</b>ual <b>use</b>rs <b>exper</b>ience <b>i</b>n <b>the</b>ir <b>brow</b>sers.

### Health check
<b>A</b> <b>peri</b>odic <b>te</b>st (<b>Hyper</b>text <b>Tran</b>sfer <b>Prot</b>ocol [HTTP] 200 <b>o</b>n `/health`, <b>Transm</b>ission <b>Cont</b>rol <b>Prot</b>ocol [TCP] <b>conn</b>ect) <b>us</b>ed <b>b</b>y <b>lo</b>ad <b>balan</b>cers, <b>Rou</b>te 53 <b>a</b>nd <b>Au</b>to <b>Scal</b>ing <b>t</b>o <b>dec</b>ide <b>whet</b>her <b>somet</b>hing <b>rece</b>ives <b>traf</b>fic <b>o</b>r <b>ge</b>ts <b>repl</b>aced.

### Remediation
<b>Fix</b>ing <b>a</b> <b>dete</b>cted <b>prob</b>lem. **Automated remediation** = <b>a</b> <b>ru</b>le <b>dete</b>cts <b>i</b>t <b>a</b>nd <b>a</b> <b>runb</b>ook <b>o</b>r <b>Lam</b>bda <b>func</b>tion <b>fix</b>es <b>i</b>t <b>with</b>out <b>a</b> <b>hum</b>an (<b>e</b>.g., <b>Con</b>fig <b>dete</b>cts <b>a</b>n <b>op</b>en <b>secu</b>rity <b>gro</b>up → <b>Syst</b>ems <b>Mana</b>ger [SSM] <b>Autom</b>ation <b>clo</b>ses <b>i</b>t).

### Runbook and playbook
<b>A</b> **runbook** <b>i</b>s <b>a</b> <b>st</b>ep-<b>b</b>y-<b>st</b>ep <b>proce</b>dure <b>f</b>or <b>a</b> <b>rout</b>ine <b>ta</b>sk (<b>rest</b>art <b>serv</b>ice, <b>rot</b>ate <b>l</b>og). <b>A</b> **playbook** <b>i</b>s <b>a</b> <b>proce</b>dure <b>f</b>or <b>investi</b>gating <b>a</b>n <b>inci</b>dent. <b>I</b>n <b>Ama</b>zon <b>W</b>eb <b>Serv</b>ices (AWS), **Systems Manager Automation runbooks** (<b>docum</b>ents) <b>ma</b>ke <b>runb</b>ooks <b>execu</b>table.

### Incident, MTTD and MTTR
<b>A</b>n **incident** <b>i</b>s <b>a</b>n <b>unpla</b>nned <b>interr</b>uption. **MTTD** (<b>me</b>an <b>ti</b>me <b>t</b>o <b>det</b>ect) <b>a</b>nd **MTTR** (<b>me</b>an <b>ti</b>me <b>t</b>o <b>reco</b>ver/<b>rep</b>air) <b>meas</b>ure <b>opera</b>tions <b>qual</b>ity; <b>go</b>od <b>ala</b>rms <b>low</b>er MTTD, <b>autom</b>ation <b>low</b>ers MTTR.

---

## Automation and infrastructure concepts

### Infrastructure as Code (IaC)
<b>Descr</b>ibing <b>infrast</b>ructure <b>i</b>n <b>fil</b>es (<b>templ</b>ates/<b>co</b>de) <b>s</b>o <b>i</b>t <b>c</b>an <b>b</b>e <b>crea</b>ted, <b>chan</b>ged <b>a</b>nd <b>dele</b>ted <b>repea</b>tably, <b>revi</b>ewed <b>i</b>n <b>G</b>it <b>a</b>nd <b>rol</b>led <b>ba</b>ck. <b>CloudFo</b>rmation, <b>Clo</b>ud <b>Develo</b>pment <b>K</b>it (CDK) <b>a</b>nd <b>Terra</b>form <b>a</b>re <b>Infrast</b>ructure <b>a</b>s <b>Co</b>de (IaC) <b>too</b>ls.

### Declarative vs imperative
**Declarative** = <b>desc</b>ribe <b>t</b>he <b>desi</b>red <b>e</b>nd <b>sta</b>te; <b>t</b>he <b>to</b>ol <b>wor</b>ks <b>o</b>ut <b>t</b>he <b>ste</b>ps (<b>CloudFo</b>rmation, <b>Terra</b>form). **Imperative** = <b>li</b>st <b>t</b>he <b>ste</b>ps <b>t</b>o <b>r</b>un (<b>scri</b>pts, <b>Comm</b>and <b>Li</b>ne <b>Inter</b>face [CLI] <b>comm</b>ands).

### Drift
<b>Wh</b>en <b>re</b>al <b>resou</b>rces <b>n</b>o <b>lon</b>ger <b>mat</b>ch <b>the</b>ir IaC <b>defin</b>ition <b>beca</b>use <b>some</b>one <b>chan</b>ged <b>th</b>em <b>b</b>y <b>ha</b>nd. <b>Dri</b>ft <b>detec</b>tion <b>fin</b>ds <b>i</b>t.

### Idempotent
<b>Runn</b>ing <b>a</b>n <b>opera</b>tion <b>twi</b>ce <b>giv</b>es <b>t</b>he <b>sa</b>me <b>res</b>ult <b>a</b>s <b>on</b>ce. IaC <b>a</b>nd <b>go</b>od <b>autom</b>ation <b>scri</b>pts <b>a</b>re <b>idemp</b>otent, <b>s</b>o <b>retr</b>ies <b>a</b>re <b>sa</b>fe.

### Desired state configuration
<b>Contin</b>uously <b>enfor</b>cing <b>th</b>at <b>mach</b>ines <b>st</b>ay <b>i</b>n <b>a</b> <b>defi</b>ned <b>configu</b>ration (<b>e</b>.g., <b>age</b>nt <b>insta</b>lled, <b>po</b>rt <b>clo</b>sed), <b>r</b>e-<b>appl</b>ying <b>i</b>t <b>i</b>f <b>i</b>t <b>dri</b>fts (<b>Syst</b>ems <b>Mana</b>ger <b>Sta</b>te <b>Mana</b>ger).

### Golden image (golden AMI)
<b>A</b> <b>p</b>re-<b>bui</b>lt, <b>patc</b>hed, <b>hard</b>ened <b>mach</b>ine <b>ima</b>ge <b>wi</b>th <b>stan</b>dard <b>soft</b>ware <b>bak</b>ed <b>i</b>n. <b>Insta</b>nces <b>laun</b>ched <b>fr</b>om <b>i</b>t <b>sta</b>rt <b>fa</b>st <b>a</b>nd <b>consi</b>stent. <b>Rebu</b>ilt <b>o</b>n <b>a</b> <b>sche</b>dule <b>a</b>s <b>n</b>ew <b>patc</b>hes <b>arr</b>ive (<b>Elas</b>tic <b>Comp</b>ute <b>Clo</b>ud [EC2] <b>Ima</b>ge <b>Buil</b>der).

### Immutable vs mutable infrastructure
**Mutable**: <b>pat</b>ch <b>a</b>nd <b>cha</b>nge <b>serv</b>ers <b>i</b>n <b>pla</b>ce. **Immutable**: <b>nev</b>er <b>cha</b>nge <b>runn</b>ing <b>serv</b>ers; <b>bui</b>ld <b>a</b> <b>n</b>ew <b>ima</b>ge <b>a</b>nd <b>repl</b>ace <b>th</b>em. <b>Immut</b>able <b>avo</b>ids <b>configu</b>ration <b>dri</b>ft <b>a</b>nd <b>mak</b>es <b>roll</b>back <b>sim</b>ple.

### Deployment strategies
- **All at once / in place**: <b>upd</b>ate <b>eve</b>ry <b>ser</b>ver <b>toge</b>ther; <b>fast</b>est, <b>h</b>as <b>down</b>time <b>ri</b>sk.
- **Rolling**: <b>upd</b>ate <b>i</b>n <b>batc</b>hes; <b>capa</b>city <b>tempor</b>arily <b>redu</b>ced.
- **Rolling with additional batch**: <b>lau</b>nch <b>ext</b>ra <b>capa</b>city <b>fir</b>st <b>s</b>o <b>fu</b>ll <b>capa</b>city <b>i</b>s <b>ke</b>pt.
- **Immutable**: <b>lau</b>nch <b>a</b> <b>fu</b>ll <b>n</b>ew <b>s</b>et <b>o</b>f <b>insta</b>nces, <b>swi</b>tch <b>wh</b>en <b>heal</b>thy.
- **Blue/green**: <b>t</b>wo <b>fu</b>ll <b>enviro</b>nments; <b>swi</b>tch <b>traf</b>fic <b>fr</b>om <b>bl</b>ue (<b>o</b>ld) <b>t</b>o <b>gre</b>en (<b>n</b>ew); <b>inst</b>ant <b>roll</b>back.
- **Canary**: <b>se</b>nd <b>a</b> <b>sma</b>ll % <b>o</b>f <b>traf</b>fic <b>t</b>o <b>t</b>he <b>n</b>ew <b>vers</b>ion, <b>th</b>en <b>t</b>he <b>re</b>st.
- **Linear**: <b>shi</b>ft <b>traf</b>fic <b>i</b>n <b>equ</b>al <b>ste</b>ps <b>ov</b>er <b>ti</b>me.

### Configuration management
<b>Keep</b>ing <b>soft</b>ware <b>sett</b>ings <b>a</b>nd <b>insta</b>lled <b>pack</b>ages <b>consi</b>stent <b>acr</b>oss <b>fle</b>ets (<b>Syst</b>ems <b>Mana</b>ger, <b>Ansi</b>ble, <b>Ch</b>ef).

### Git basics
**Git** <b>i</b>s <b>vers</b>ion <b>cont</b>rol: <b>a</b> **repository** <b>hol</b>ds <b>fil</b>es <b>a</b>nd <b>the</b>ir <b>hist</b>ory; <b>a</b> **commit** <b>i</b>s <b>a</b> <b>sav</b>ed <b>cha</b>nge; <b>a</b> **branch** <b>i</b>s <b>a</b> <b>sepa</b>rate <b>li</b>ne <b>o</b>f <b>wo</b>rk; <b>a</b> **pull request (merge request)** <b>prop</b>oses <b>merg</b>ing <b>a</b> <b>bra</b>nch <b>aft</b>er <b>rev</b>iew. **GitOps** <b>mea</b>ns <b>t</b>he <b>G</b>it <b>repos</b>itory <b>i</b>s <b>t</b>he <b>sou</b>rce <b>o</b>f <b>tru</b>th <b>a</b>nd <b>pipel</b>ines <b>app</b>ly <b>wha</b>t's <b>mer</b>ged.

### Terraform basics
<b>HashiC</b>orp's IaC <b>to</b>ol <b>usi</b>ng HCL (<b>Hashi</b>Corp <b>Configu</b>ration <b>Lang</b>uage). **Providers** <b>ta</b>lk <b>t</b>o <b>Applic</b>ation <b>Progra</b>mming <b>Inter</b>faces [APIs] (<b>t</b>he AWS <b>prov</b>ider). **State** <b>reco</b>rds <b>wh</b>at <b>Terra</b>form <b>crea</b>ted; <b>sto</b>re <b>i</b>t <b>remo</b>tely <b>i</b>n **S3** <b>wi</b>th **state locking** (<b>Dyna</b>moDB <b>tab</b>le <b>o</b>r S3 <b>nat</b>ive <b>lo</b>ck <b>fi</b>le) <b>s</b>o <b>t</b>wo <b>peo</b>ple <b>can</b>'t <b>app</b>ly <b>a</b>t <b>on</b>ce. `terraform plan` <b>prev</b>iews, `terraform apply` <b>chan</b>ges, **modules** <b>a</b>re <b>reus</b>able <b>buil</b>ding <b>blo</b>cks.

### YAML and JSON
<b>T</b>wo <b>te</b>xt <b>form</b>ats <b>f</b>or <b>struc</b>tured <b>da</b>ta. <b>CloudFo</b>rmation <b>templ</b>ates, <b>poli</b>cies <b>a</b>nd SSM <b>docum</b>ents <b>u</b>se <b>th</b>em. YAML <b>Ain</b>'t <b>Mar</b>kup <b>Lang</b>uage (YAML) <b>rel</b>ies <b>o</b>n <b>indent</b>ation (<b>mist</b>akes <b>cau</b>se <b>temp</b>late <b>err</b>ors); JSON <b>us</b>es <b>bra</b>ces <b>a</b>nd <b>quo</b>tes.

### Cron and rate expressions
<b>Sche</b>dule <b>form</b>ats. **Cron**: `cron(0 3 * * ? *)` = 03:00 UTC <b>dai</b>ly (AWS <b>cr</b>on <b>h</b>as <b>s</b>ix <b>fie</b>lds: <b>minu</b>tes, <b>hou</b>rs, <b>d</b>ay-<b>o</b>f-<b>mon</b>th, <b>mon</b>th, <b>d</b>ay-<b>o</b>f-<b>we</b>ek, <b>ye</b>ar). **Rate**: `rate(5 minutes)`.

### Tags
<b>K</b>ey-<b>val</b>ue <b>lab</b>els <b>o</b>n <b>resou</b>rces (`Environment=prod`). <b>Us</b>ed <b>f</b>or <b>co</b>st <b>alloc</b>ation, <b>autom</b>ation <b>targe</b>ting (<b>pat</b>ch <b>a</b>ll `PatchGroup=web`), <b>acc</b>ess <b>cont</b>rol (ABAC) <b>a</b>nd <b>organi</b>zation.

---

## Reliability concepts

### Scalability vs elasticity
**Scalability**: <b>t</b>he <b>sys</b>tem *<b>c</b>an* <b>gr</b>ow <b>t</b>o <b>han</b>dle <b>mo</b>re <b>lo</b>ad. **Elasticity**: <b>i</b>t <b>gro</b>ws *<b>a</b>nd <b>shri</b>nks* <b>automat</b>ically <b>wi</b>th <b>dem</b>and.

### Horizontal vs vertical scaling
**Horizontal** = <b>mo</b>re <b>insta</b>nces (<b>sca</b>le <b>o</b>ut/<b>i</b>n). **Vertical** = <b>big</b>ger <b>inst</b>ance (<b>sca</b>le <b>u</b>p/<b>do</b>wn), <b>usua</b>lly <b>nee</b>ds <b>a</b> <b>st</b>op/<b>sta</b>rt.

### High availability vs fault tolerance
**High availability**: <b>reco</b>vers <b>quic</b>kly <b>fr</b>om <b>fail</b>ure, <b>sho</b>rt <b>interr</b>uption <b>poss</b>ible. **Fault tolerance**: <b>kee</b>ps <b>work</b>ing <b>wi</b>th <b>n</b>o <b>interr</b>uption <b>beca</b>use <b>redun</b>dant <b>capa</b>city <b>i</b>s <b>alre</b>ady <b>runn</b>ing.

### Multi-AZ
<b>Runn</b>ing <b>cop</b>ies <b>i</b>n <b>t</b>wo <b>o</b>r <b>mo</b>re <b>Availa</b>bility <b>Zon</b>es (<b>sepa</b>rate <b>da</b>ta <b>cent</b>ers <b>i</b>n <b>o</b>ne <b>Reg</b>ion) <b>s</b>o <b>o</b>ne <b>da</b>ta-<b>cen</b>ter <b>fail</b>ure <b>does</b>n't <b>ta</b>ke <b>t</b>he <b>work</b>load <b>do</b>wn.

### RPO and RTO
**RPO (Recovery Point Objective)**: <b>maxi</b>mum <b>accep</b>table <b>da</b>ta <b>lo</b>ss, <b>meas</b>ured <b>i</b>n <b>ti</b>me. **RTO (Recovery Time Objective)**: <b>maxi</b>mum <b>accep</b>table <b>down</b>time. <b>Bac</b>kup <b>frequ</b>ency <b>dri</b>ves RPO; <b>rest</b>ore <b>spe</b>ed <b>a</b>nd <b>stan</b>dby <b>resou</b>rces <b>dri</b>ve RTO.

### Backup vs snapshot vs replication
<b>A</b> **snapshot** <b>i</b>s <b>a</b> <b>poi</b>nt-<b>i</b>n-<b>ti</b>me <b>co</b>py <b>o</b>f <b>a</b> <b>vol</b>ume/DB. <b>A</b> **backup** <b>i</b>s <b>a</b>ny <b>resto</b>rable <b>co</b>py <b>ke</b>pt <b>f</b>or <b>a</b> <b>reten</b>tion <b>per</b>iod (<b>snaps</b>hots <b>a</b>re <b>o</b>ne <b>ki</b>nd). **Replication** <b>contin</b>uously <b>cop</b>ies <b>da</b>ta <b>elsew</b>here. <b>Replic</b>ation <b>alo</b>ne <b>isn</b>'t <b>a</b> <b>bac</b>kup: <b>dele</b>tes <b>a</b>nd <b>corru</b>ption <b>repli</b>cate <b>t</b>oo.

### Point-in-time recovery (PITR)
<b>Resto</b>ring <b>a</b> <b>data</b>base <b>t</b>o <b>a</b>ny <b>sec</b>ond <b>wit</b>hin <b>a</b> <b>reten</b>tion <b>win</b>dow, <b>usi</b>ng <b>a</b> <b>fu</b>ll <b>bac</b>kup <b>pl</b>us <b>transa</b>ction <b>lo</b>gs.

### Versioning
<b>Keep</b>ing <b>prev</b>ious <b>vers</b>ions <b>o</b>f <b>obje</b>cts/<b>fil</b>es <b>s</b>o <b>overwr</b>itten <b>o</b>r <b>dele</b>ted <b>da</b>ta <b>c</b>an <b>b</b>e <b>recov</b>ered.

### Disaster recovery strategies
<b>Fr</b>om <b>chea</b>pest/<b>slow</b>est <b>t</b>o <b>mo</b>st <b>expen</b>sive/<b>fast</b>est: **backup and restore**, **pilot light** (<b>da</b>ta <b>repli</b>cated, <b>serv</b>ers <b>o</b>ff), **warm standby** (<b>sma</b>ll <b>work</b>ing <b>co</b>py <b>runn</b>ing), **active/active** (<b>fu</b>ll <b>capa</b>city <b>i</b>n <b>mult</b>iple <b>Regi</b>ons).

### Caching
<b>Keep</b>ing <b>cop</b>ies <b>o</b>f <b>frequ</b>ently <b>us</b>ed <b>da</b>ta <b>i</b>n <b>a</b> <b>fas</b>ter <b>lay</b>er (<b>mem</b>ory, <b>ed</b>ge) <b>t</b>o <b>red</b>uce <b>late</b>ncy <b>a</b>nd <b>lo</b>ad. **TTL** (<b>ti</b>me <b>t</b>o <b>li</b>ve) <b>cont</b>rols <b>h</b>ow <b>lo</b>ng <b>a</b> <b>cac</b>hed <b>it</b>em <b>i</b>s <b>val</b>id. **Cache hit ratio** = <b>sha</b>re <b>o</b>f <b>requ</b>ests <b>ser</b>ved <b>fr</b>om <b>cac</b>he; <b>hig</b>her <b>i</b>s <b>bet</b>ter.

### Throttling
<b>A</b> <b>serv</b>ice <b>rejec</b>ting <b>requ</b>ests <b>abo</b>ve <b>i</b>ts <b>ra</b>te <b>lim</b>it (HTTP 429 <b>o</b>r `ThrottlingException`). <b>F</b>ix <b>wi</b>th <b>retr</b>ies <b>wi</b>th **exponential backoff and jitter**, <b>requ</b>est <b>quo</b>ta <b>incre</b>ases, <b>o</b>r <b>smoot</b>hing <b>traf</b>fic (<b>que</b>ues).

### Service quotas
<b>P</b>er-<b>acco</b>unt, <b>p</b>er-<b>Reg</b>ion <b>lim</b>its (<b>e</b>.g., <b>virt</b>ual CPU [vCPU] <b>lim</b>its <b>f</b>or <b>O</b>n-<b>Dem</b>and <b>insta</b>nces, <b>num</b>ber <b>o</b>f <b>Virt</b>ual <b>Priv</b>ate <b>Clo</b>uds [VPCs]). <b>Ma</b>ny <b>a</b>re <b>adjus</b>table <b>v</b>ia **Service Quotas**; <b>so</b>me <b>a</b>re <b>ha</b>rd <b>lim</b>its.

---

## Storage and performance concepts

### Block, file and object storage
**Block** (EBS): <b>a</b> <b>di</b>sk <b>f</b>or <b>o</b>ne <b>inst</b>ance. **File** (<b>Elas</b>tic <b>Fi</b>le <b>Sys</b>tem [EFS], FSx, S3 <b>Fil</b>es): <b>a</b> <b>sha</b>red <b>fi</b>le <b>sys</b>tem <b>moun</b>ted <b>b</b>y <b>ma</b>ny <b>clie</b>nts. **Object** (S3): <b>fil</b>es <b>sto</b>red <b>b</b>y <b>k</b>ey <b>v</b>ia HTTP <b>Applic</b>ation <b>Progra</b>mming <b>Inter</b>face (API).

### IOPS, throughput and latency
**Input/Output Operations Per Second (IOPS)**: <b>num</b>ber <b>o</b>f <b>re</b>ad/<b>wri</b>te <b>opera</b>tions <b>p</b>er <b>sec</b>ond (<b>sma</b>ll <b>ran</b>dom <b>I</b>/<b>O</b>, <b>datab</b>ases). **Throughput**: MB/<b>s</b> <b>mov</b>ed (<b>lar</b>ge <b>seque</b>ntial <b>I</b>/<b>O</b>). **Latency**: <b>ti</b>me <b>p</b>er <b>opera</b>tion. **Queue length**: <b>requ</b>ests <b>wait</b>ing; <b>a</b> <b>lo</b>ng <b>que</b>ue <b>mea</b>ns <b>t</b>he <b>vol</b>ume <b>can</b>'t <b>ke</b>ep <b>u</b>p.

### Burst credits
<b>So</b>me <b>resou</b>rces (<b>T</b> <b>insta</b>nces, gp2 <b>volu</b>mes, EFS <b>Burs</b>ting <b>mo</b>de, st1/sc1) <b>ea</b>rn <b>cred</b>its <b>whi</b>le <b>id</b>le <b>a</b>nd <b>spe</b>nd <b>th</b>em <b>t</b>o <b>exc</b>eed <b>the</b>ir <b>base</b>line. <b>Wh</b>en <b>cred</b>its <b>r</b>un <b>o</b>ut, <b>perfor</b>mance <b>dro</b>ps <b>t</b>o <b>base</b>line: <b>a</b> <b>clas</b>sic "<b>i</b>t <b>w</b>as <b>fa</b>st, <b>th</b>en <b>sudd</b>enly <b>sl</b>ow" <b>cau</b>se.

### Lazy loading from snapshots
<b>A</b> <b>vol</b>ume <b>rest</b>ored <b>fr</b>om <b>a</b> <b>snap</b>shot <b>pul</b>ls <b>blo</b>cks <b>fr</b>om S3 <b>t</b>he <b>fir</b>st <b>ti</b>me <b>they</b>'re <b>re</b>ad, <b>s</b>o <b>fir</b>st <b>acc</b>ess <b>i</b>s <b>sl</b>ow. <b>F</b>ix <b>b</b>y <b>read</b>ing <b>eve</b>ry <b>blo</b>ck <b>on</b>ce (<b>initial</b>ization) <b>o</b>r <b>enab</b>ling <b>Fa</b>st <b>Snap</b>shot <b>Rest</b>ore.

### Connection pooling
<b>Reus</b>ing <b>a</b> <b>s</b>et <b>o</b>f <b>op</b>en <b>data</b>base <b>connec</b>tions <b>inst</b>ead <b>o</b>f <b>open</b>ing <b>n</b>ew <b>on</b>es <b>p</b>er <b>requ</b>est; <b>prot</b>ects <b>datab</b>ases <b>fr</b>om <b>conne</b>ction <b>sto</b>rms (<b>Relat</b>ional <b>Data</b>base <b>Serv</b>ice [RDS] <b>Pro</b>xy).

---

## Networking concepts

### CIDR and subnet sizing
`10.0.1.0/24` = 256 <b>addre</b>sses; AWS <b>rese</b>rves 5 <b>p</b>er <b>sub</b>net, <b>s</b>o 251 <b>usa</b>ble. <b>Smal</b>ler <b>pre</b>fix <b>num</b>ber = <b>big</b>ger <b>ran</b>ge. <b>Runn</b>ing <b>o</b>ut <b>o</b>f IP <b>addre</b>sses (IPs) <b>i</b>n <b>a</b> <b>sub</b>net ("<b>insuff</b>icient <b>fr</b>ee <b>addre</b>sses") <b>blo</b>cks <b>laun</b>ches, <b>Lam</b>bda <b>Elas</b>tic <b>Netw</b>ork <b>Inter</b>faces (ENIs), <b>Elas</b>tic <b>Kuber</b>netes <b>Serv</b>ice (EKS) <b>po</b>ds <b>a</b>nd <b>lo</b>ad <b>bala</b>ncer <b>scal</b>ing. <b>F</b>ix: <b>lar</b>ger/<b>addit</b>ional <b>subn</b>ets, <b>secon</b>dary <b>Class</b>less <b>Int</b>er-<b>Dom</b>ain <b>Rout</b>ing (CIDR) <b>o</b>n <b>t</b>he <b>Virt</b>ual <b>Priv</b>ate <b>Clo</b>ud (VPC).

### Route table and longest prefix match
<b>Ea</b>ch <b>sub</b>net <b>us</b>es <b>a</b> <b>rou</b>te <b>tab</b>le. <b>T</b>he **most specific** <b>matc</b>hing <b>rou</b>te <b>wi</b>ns (`10.0.1.0/24` <b>bea</b>ts `10.0.0.0/16` <b>bea</b>ts `0.0.0.0/0`).

### Public vs private subnet
<b>A</b> **public** <b>sub</b>net <b>rou</b>tes `0.0.0.0/0` <b>t</b>o <b>a</b>n <b>inte</b>rnet <b>gate</b>way (<b>insta</b>nces <b>al</b>so <b>ne</b>ed <b>a</b> <b>pub</b>lic <b>Inte</b>rnet <b>Prot</b>ocol [IP]). <b>A</b> **private** <b>sub</b>net <b>does</b>n't; <b>i</b>t <b>reac</b>hes <b>t</b>he <b>inte</b>rnet <b>thro</b>ugh <b>a</b> <b>Netw</b>ork <b>Addr</b>ess <b>Transl</b>ation (NAT) <b>gate</b>way.

### Stateful vs stateless filtering
**Stateful** (<b>secu</b>rity <b>gro</b>ups): <b>ret</b>urn <b>traf</b>fic <b>i</b>s <b>automat</b>ically <b>allo</b>wed. **Stateless** (<b>netw</b>ork <b>Acc</b>ess <b>Cont</b>rol <b>Lis</b>ts [ACLs]): <b>y</b>ou <b>mu</b>st <b>all</b>ow <b>ret</b>urn <b>traf</b>fic <b>your</b>self, <b>usua</b>lly **ephemeral ports 1024–65535**.

### Ports and protocols
TCP (<b>reli</b>able), <b>Us</b>er <b>Data</b>gram <b>Prot</b>ocol [UDP] (<b>fa</b>st, <b>connect</b>ionless), <b>Inte</b>rnet <b>Cont</b>rol <b>Mess</b>age <b>Prot</b>ocol [ICMP] (<b>pi</b>ng). <b>Com</b>mon <b>por</b>ts: 22 <b>Sec</b>ure <b>She</b>ll (SSH), 80 HTTP, 443 <b>Hyper</b>text <b>Tran</b>sfer <b>Prot</b>ocol <b>Sec</b>ure (HTTPS), 3389 <b>Rem</b>ote <b>Desk</b>top <b>Prot</b>ocol (RDP), 3306 MySQL, 5432 <b>Postg</b>reSQL, 2049 <b>Netw</b>ork <b>Fi</b>le <b>Sys</b>tem (NFS), 53 <b>Dom</b>ain <b>Na</b>me <b>Sys</b>tem (DNS), 6379 <b>Red</b>is.

### DNS, records and TTL
DNS <b>tur</b>ns <b>nam</b>es <b>in</b>to IPs. <b>Rec</b>ord TTL <b>cont</b>rols <b>h</b>ow <b>lo</b>ng <b>resol</b>vers <b>cac</b>he <b>answ</b>ers; <b>aft</b>er <b>a</b> <b>cha</b>nge, <b>o</b>ld <b>answ</b>ers <b>c</b>an <b>pers</b>ist <b>unt</b>il <b>t</b>he TTL <b>expi</b>res.

### HTTP status codes
**2xx** <b>succ</b>ess; **3xx** <b>redi</b>rect; **4xx** <b>cli</b>ent-<b>si</b>de <b>prob</b>lem (400 <b>b</b>ad <b>requ</b>est, **401** <b>n</b>ot <b>authent</b>icated, **403** <b>forbi</b>dden/<b>den</b>ied, **404** <b>n</b>ot <b>fou</b>nd, **429** <b>throt</b>tled); **5xx** <b>ser</b>ver-<b>si</b>de <b>prob</b>lem (**500** <b>inte</b>rnal <b>err</b>or, **502** <b>b</b>ad <b>gate</b>way: <b>t</b>he <b>pro</b>xy <b>g</b>ot <b>a</b>n <b>inva</b>lid <b>resp</b>onse <b>fr</b>om <b>t</b>he <b>back</b>end, **503** <b>serv</b>ice <b>unavai</b>lable: <b>n</b>o <b>heal</b>thy <b>capa</b>city, **504** <b>gate</b>way <b>time</b>out: <b>t</b>he <b>back</b>end <b>to</b>ok <b>t</b>oo <b>lo</b>ng).

### TLS and certificates
<b>Trans</b>port <b>Lay</b>er <b>Secu</b>rity (TLS) <b>encr</b>ypts <b>traf</b>fic <b>i</b>n <b>tran</b>sit (HTTPS). <b>A</b> <b>certif</b>icate, <b>iss</b>ued <b>b</b>y <b>a</b> <b>certif</b>icate <b>autho</b>rity, <b>pro</b>ves <b>dom</b>ain <b>owner</b>ship; <b>i</b>t <b>expi</b>res <b>a</b>nd <b>mu</b>st <b>b</b>e <b>rene</b>wed. TLS <b>termin</b>ation <b>i</b>s <b>whe</b>re <b>traf</b>fic <b>i</b>s <b>decry</b>pted (<b>lo</b>ad <b>bala</b>ncer, <b>Cloud</b>Front, <b>inst</b>ance).

### BGP
<b>Bor</b>der <b>Gate</b>way <b>Prot</b>ocol: <b>rout</b>ers <b>exch</b>ange <b>whi</b>ch IP <b>ran</b>ges <b>th</b>ey <b>c</b>an <b>rea</b>ch. <b>Us</b>ed <b>b</b>y <b>Dir</b>ect <b>Conn</b>ect <b>a</b>nd <b>dyna</b>mic <b>Virt</b>ual <b>Priv</b>ate <b>Netw</b>orks (VPNs); <b>i</b>f <b>t</b>he <b>Bor</b>der <b>Gate</b>way <b>Prot</b>ocol (BGP) <b>sess</b>ion <b>i</b>s <b>do</b>wn, <b>rou</b>tes <b>disap</b>pear <b>a</b>nd <b>traf</b>fic <b>sto</b>ps.

### CDN, origin and cache key
<b>A</b> <b>Cont</b>ent <b>Deli</b>very <b>Netw</b>ork [CDN] (<b>Cloud</b>Front) <b>ser</b>ves <b>cac</b>hed <b>cop</b>ies <b>fr</b>om <b>ed</b>ge <b>locat</b>ions. <b>T</b>he **origin** <b>i</b>s <b>t</b>he <b>re</b>al <b>sou</b>rce (S3, <b>Applic</b>ation <b>Lo</b>ad <b>Bala</b>ncer [ALB]). <b>T</b>he **cache key** <b>i</b>s <b>wh</b>at <b>mak</b>es <b>a</b> <b>cac</b>hed <b>obj</b>ect <b>uni</b>que (<b>Unif</b>orm <b>Reso</b>urce <b>Loca</b>tor [URL] <b>pa</b>th <b>pl</b>us <b>cho</b>sen <b>head</b>ers, <b>cook</b>ies, <b>que</b>ry <b>stri</b>ngs); <b>inclu</b>ding <b>t</b>oo <b>mu</b>ch <b>low</b>ers <b>t</b>he <b>h</b>it <b>rat</b>io.

### Anycast
<b>O</b>ne IP <b>addr</b>ess <b>annou</b>nced <b>fr</b>om <b>ma</b>ny <b>locat</b>ions; <b>use</b>rs <b>rea</b>ch <b>t</b>he <b>near</b>est <b>o</b>ne (<b>Glo</b>bal <b>Accele</b>rator).

---

## Security concepts

### Authentication vs authorization
**Authentication**: <b>prov</b>ing <b>iden</b>tity (<b>pass</b>word, <b>Mul</b>ti-<b>Fac</b>tor <b>Authent</b>ication [MFA], <b>ke</b>ys). **Authorization**: <b>wh</b>at <b>a</b>n <b>iden</b>tity <b>m</b>ay <b>d</b>o (<b>poli</b>cies).

### Least privilege
<b>Gra</b>nt <b>on</b>ly <b>t</b>he <b>permis</b>sions <b>nee</b>ded, <b>th</b>en <b>tigh</b>ten <b>usi</b>ng <b>usa</b>ge <b>da</b>ta.

### Explicit vs implicit deny
<b>Every</b>thing <b>i</b>s **implicitly denied** <b>unl</b>ess <b>allo</b>wed. <b>A</b>n **explicit Deny** <b>i</b>n <b>a</b>ny <b>pol</b>icy <b>overr</b>ides <b>eve</b>ry <b>All</b>ow.

### MFA
<b>Mul</b>ti-<b>fac</b>tor <b>authent</b>ication: <b>pass</b>word <b>pl</b>us <b>a</b> <b>sec</b>ond <b>fac</b>tor (<b>authent</b>icator <b>a</b>pp, <b>hard</b>ware <b>k</b>ey).

### Federation
<b>Sign</b>ing <b>i</b>n <b>wi</b>th <b>a</b>n <b>exis</b>ting <b>iden</b>tity (<b>corpo</b>rate <b>direc</b>tory <b>v</b>ia <b>Secu</b>rity <b>Asser</b>tion <b>Mar</b>kup <b>Lang</b>uage [SAML] 2.0, <b>o</b>r OpenID <b>Conn</b>ect [OIDC] <b>provi</b>ders) <b>inst</b>ead <b>o</b>f <b>sepa</b>rate AWS <b>use</b>rs.

### Encryption at rest and in transit
<b>A</b>t <b>re</b>st: <b>da</b>ta <b>sto</b>red <b>o</b>n <b>di</b>sk <b>i</b>s <b>encry</b>pted <b>wi</b>th <b>ke</b>ys (KMS). <b>I</b>n <b>tran</b>sit: <b>da</b>ta <b>o</b>n <b>t</b>he <b>netw</b>ork <b>i</b>s <b>encry</b>pted (TLS, <b>Inte</b>rnet <b>Prot</b>ocol <b>Secu</b>rity [<b>IPs</b>ec]).

### Envelope encryption
<b>Da</b>ta <b>i</b>s <b>encry</b>pted <b>wi</b>th <b>a</b> <b>da</b>ta <b>k</b>ey; <b>t</b>he <b>da</b>ta <b>k</b>ey <b>i</b>s <b>encry</b>pted <b>wi</b>th <b>a</b> KMS <b>k</b>ey. <b>On</b>ly <b>t</b>he <b>sma</b>ll <b>da</b>ta <b>k</b>ey <b>go</b>es <b>t</b>o KMS.

### Data classification
<b>Labe</b>ling <b>da</b>ta <b>b</b>y <b>sensit</b>ivity (<b>pub</b>lic, <b>inte</b>rnal, <b>confid</b>ential, <b>restr</b>icted) <b>a</b>nd <b>appl</b>ying <b>matc</b>hing <b>cont</b>rols (<b>encry</b>ption, <b>acc</b>ess, <b>reten</b>tion). <b>Implem</b>ented <b>wi</b>th <b>ta</b>gs, <b>sepa</b>rate <b>acco</b>unts/<b>buck</b>ets, <b>Mac</b>ie <b>disco</b>very <b>a</b>nd <b>poli</b>cies <b>key</b>ed <b>o</b>n <b>ta</b>gs.

### Compliance as code / continuous compliance
<b>Rul</b>es <b>th</b>at <b>automat</b>ically <b>a</b>nd <b>contin</b>uously <b>che</b>ck <b>resou</b>rces <b>agai</b>nst <b>requir</b>ements (AWS <b>Con</b>fig <b>rul</b>es, <b>confor</b>mance <b>pac</b>ks, <b>Secu</b>rity <b>H</b>ub <b>stand</b>ards) <b>inst</b>ead <b>o</b>f <b>peri</b>odic <b>man</b>ual <b>aud</b>its.

### Detective vs preventive vs responsive controls
**Preventive** <b>sto</b>ps <b>a</b> <b>b</b>ad <b>act</b>ion (<b>Serv</b>ice <b>Cont</b>rol <b>Pol</b>icy [SCP], <b>Iden</b>tity <b>a</b>nd <b>Acc</b>ess <b>Manag</b>ement [IAM] <b>de</b>ny). **Detective** <b>fin</b>ds <b>i</b>t <b>aft</b>er <b>i</b>t <b>happ</b>ens (<b>Con</b>fig <b>ru</b>le, <b>Guard</b>Duty). **Responsive/corrective** <b>fix</b>es <b>i</b>t (<b>autom</b>ated <b>remedi</b>ation).

---

## AI in operations

### Generative AI and foundation models
<b>Lar</b>ge <b>p</b>re-<b>trai</b>ned <b>mod</b>els (<b>found</b>ation <b>mod</b>els) <b>th</b>at <b>gene</b>rate <b>te</b>xt/<b>co</b>de. **Amazon Bedrock** <b>prov</b>ides <b>th</b>em <b>thro</b>ugh <b>a</b>n API. <b>Operati</b>onally <b>y</b>ou <b>moni</b>tor <b>invoca</b>tions, <b>late</b>ncy, <b>tok</b>en <b>usa</b>ge, <b>err</b>ors <b>a</b>nd <b>throt</b>tles <b>li</b>ke <b>a</b>ny <b>oth</b>er <b>work</b>load.

### AI agents
<b>Soft</b>ware <b>th</b>at <b>us</b>es <b>a</b> <b>mod</b>el <b>t</b>o <b>pl</b>an <b>a</b>nd <b>ta</b>ke <b>mul</b>ti-<b>st</b>ep <b>acti</b>ons <b>wi</b>th <b>too</b>ls (<b>e</b>.g., <b>que</b>ry <b>lo</b>gs, <b>op</b>en <b>tick</b>ets, <b>cre</b>ate <b>a</b> <b>pu</b>ll <b>requ</b>est), <b>usua</b>lly <b>wi</b>th <b>hum</b>an <b>appr</b>oval <b>ste</b>ps. **AWS DevOps Agent** (<b>inci</b>dent <b>investi</b>gation <b>a</b>nd <b>opera</b>tions), **AWS Security Agent** (<b>penetr</b>ation <b>test</b>ing <b>a</b>nd <b>co</b>de <b>secu</b>rity; <b>n</b>ow <b>mark</b>eted <b>und</b>er AWS <b>Conti</b>nuum) <b>a</b>nd **Kiro** (<b>agen</b>tic <b>develo</b>pment <b>enviro</b>nment) <b>a</b>re AWS <b>exam</b>ples <b>i</b>n <b>t</b>he SOA-C03 <b>sco</b>pe.

### MCP
<b>Mod</b>el <b>Cont</b>ext <b>Prot</b>ocol: <b>a</b>n <b>op</b>en <b>stan</b>dard <b>f</b>or <b>conne</b>cting <b>Artif</b>icial <b>Intell</b>igence (AI) <b>age</b>nts <b>t</b>o <b>too</b>ls <b>a</b>nd <b>da</b>ta <b>sour</b>ces.
