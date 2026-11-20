# AWS Certified CloudOps Engineer – Associate (SOA-C03) Study Notes

<b>Se</b>lf-<b>conta</b>ined <b>stu</b>dy <b>not</b>es <b>f</b>or SOA-C03 (<b>form</b>erly <b>Sys</b>Ops <b>Adminis</b>trator – <b>Assoc</b>iate), <b>bui</b>lt <b>fr</b>om <b>t</b>he <b>offi</b>cial <b>ex</b>am <b>gui</b>de (v1.2, <b>Octo</b>ber 2026) <b>a</b>nd <b>i</b>ts <b>i</b>n-<b>sco</b>pe <b>serv</b>ice <b>li</b>st. <b>Eve</b>ry <b>te</b>rm <b>i</b>s <b>defi</b>ned <b>whe</b>re <b>it</b>'s <b>us</b>ed.

## How to study with this repo

1. **Skim [00-cloudops-basics.md](00-cloudops-basics.md)** <b>f</b>or <b>t</b>he <b>opera</b>tions <b>vocab</b>ulary: <b>metr</b>ics <b>v</b>s <b>lo</b>gs <b>v</b>s <b>tra</b>ces, <b>ala</b>rms, <b>runb</b>ooks, IaC, <b>deplo</b>yment <b>strat</b>egies, <b>bur</b>st <b>cred</b>its, HTTP <b>sta</b>tus <b>cod</b>es, <b>stat</b>eful <b>v</b>s <b>state</b>less <b>filte</b>ring, <b>a</b>nd <b>mo</b>re. <b>Dom</b>ain <b>fil</b>es <b>li</b>nk <b>ba</b>ck <b>t</b>o <b>i</b>t.
2. **Work through the five domain files.** <b>Ea</b>ch <b>sta</b>rts <b>wi</b>th <b>t</b>he <b>offi</b>cial <b>tas</b>ks/<b>ski</b>lls, <b>th</b>en <b>cov</b>ers <b>eve</b>ry <b>serv</b>ice <b>a</b>nd <b>feat</b>ure <b>wi</b>th: <b>wh</b>at <b>i</b>t <b>i</b>s <b>a</b>nd <b>k</b>ey <b>ter</b>ms, <b>h</b>ow <b>t</b>o <b>confi</b>gure <b>i</b>t, <b>h</b>ow <b>t</b>o <b>troubl</b>eshoot <b>i</b>t, <b>a</b>nd **exam triggers** (<b>scen</b>ario <b>wor</b>ds → <b>ans</b>wer). <b>Ea</b>ch <b>en</b>ds <b>wi</b>th <b>gotc</b>has <b>a</b>nd <b>a</b> <b>qui</b>ck-<b>rec</b>all <b>summ</b>ary.
3. **Review [06-cheat-sheet.md](06-cheat-sheet.md)** <b>bef</b>ore <b>t</b>he <b>ex</b>am: <b>deci</b>sion <b>tab</b>les <b>p</b>er <b>dom</b>ain, <b>t</b>op <b>tra</b>ps, <b>numb</b>ers.
4. **Look up abbreviations in [00-abbreviations-glossary.md](00-abbreviations-glossary.md).** <b>Ea</b>ch <b>i</b>s <b>al</b>so <b>spel</b>led <b>o</b>ut <b>o</b>n <b>fir</b>st <b>u</b>se <b>i</b>n <b>eve</b>ry <b>fi</b>le; <b>ins</b>ide <b>brac</b>kets <b>t</b>he <b>abbrev</b>iation <b>us</b>es <b>squ</b>are <b>brac</b>kets, <b>e</b>.g. *(<b>Virt</b>ual <b>Priv</b>ate <b>Clo</b>ud [VPC])*.

## Files

| <b>Fi</b>le | <b>Dom</b>ain | <b>Wei</b>ght |
|---|---|---|
| [00-cloudops-basics.md](00-cloudops-basics.md) | <b>T</b>he 101 <b>lay</b>er <b>f</b>or <b>opera</b>tions | — |
| [01-monitoring-logging-remediation-performance.md](01-monitoring-logging-remediation-performance.md) | <b>Dom</b>ain 1: <b>Monit</b>oring, <b>Logg</b>ing, <b>Anal</b>ysis, <b>Remedi</b>ation, <b>a</b>nd <b>Perfor</b>mance <b>Optimi</b>zation | 22% |
| [02-reliability-business-continuity.md](02-reliability-business-continuity.md) | <b>Dom</b>ain 2: <b>Reliab</b>ility <b>a</b>nd <b>Busi</b>ness <b>Conti</b>nuity | 22% |
| [03-deployment-provisioning-automation.md](03-deployment-provisioning-automation.md) | <b>Dom</b>ain 3: <b>Deplo</b>yment, <b>Provis</b>ioning, <b>a</b>nd <b>Autom</b>ation | 22% |
| [04-security-compliance.md](04-security-compliance.md) | <b>Dom</b>ain 4: <b>Secu</b>rity <b>a</b>nd <b>Compl</b>iance | 16% |
| [05-networking-content-delivery.md](05-networking-content-delivery.md) | <b>Dom</b>ain 5: <b>Netwo</b>rking <b>a</b>nd <b>Cont</b>ent <b>Deli</b>very | 18% |
| [06-cheat-sheet.md](06-cheat-sheet.md) | <b>Deci</b>sion <b>tab</b>les, <b>tra</b>ps, <b>numb</b>ers | — |
| [00-abbreviations-glossary.md](00-abbreviations-glossary.md) | <b>A</b>–<b>Z</b> <b>abbrevi</b>ations | — |

## Overlap tags

`[also: DOP]` (<b>Dev</b>Ops <b>Engi</b>neer <b>Profes</b>sional), `[also: SAA]` (<b>Solut</b>ions <b>Archi</b>tect <b>Assoc</b>iate), `[also: ANS]` (<b>Adva</b>nced <b>Netwo</b>rking) <b>ma</b>rk <b>mate</b>rial <b>sha</b>red <b>wi</b>th <b>tho</b>se <b>exa</b>ms. <b>Every</b>thing <b>i</b>s <b>sti</b>ll <b>cove</b>red <b>i</b>n <b>fu</b>ll <b>he</b>re.

## Exam at a glance

- 65 <b>quest</b>ions (50 <b>sco</b>red, 15 <b>unsc</b>ored), 130 <b>minu</b>tes, <b>pass</b>ing <b>sco</b>re 720 / 1000; <b>mult</b>iple <b>cho</b>ice <b>a</b>nd <b>mult</b>iple <b>resp</b>onse. <b>N</b>o <b>ex</b>am <b>la</b>bs <b>a</b>re <b>lis</b>ted <b>i</b>n <b>t</b>he <b>curr</b>ent <b>gui</b>de.
- <b>N</b>ew <b>i</b>n SOA-C03 <b>v</b>s C02: <b>Cloud</b>Watch <b>age</b>nt <b>f</b>or ECS/EKS, CDK, <b>compl</b>iance <b>enforc</b>ement (<b>Regi</b>ons/<b>serv</b>ices), <b>Cloud</b>Watch <b>netw</b>ork <b>monit</b>oring <b>serv</b>ices, AI-<b>rela</b>ted <b>serv</b>ices (<b>Bedr</b>ock <b>monit</b>oring, AWS <b>Dev</b>Ops <b>Age</b>nt, <b>Ki</b>ro, AWS <b>Secu</b>rity <b>Age</b>nt), <b>Ama</b>zon S3 <b>Fil</b>es. <b>T</b>he <b>o</b>ld <b>co</b>st/<b>perfor</b>mance <b>dom</b>ain <b>w</b>as <b>fol</b>ded <b>in</b>to <b>Dom</b>ain 1.
- <b>Offi</b>cial <b>gui</b>de: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03.html

> <b>Numb</b>ers <b>a</b>nd <b>quo</b>tas <b>cha</b>nge <b>ov</b>er <b>ti</b>me. <b>Whe</b>re AWS <b>rai</b>sed <b>a</b> <b>lim</b>it <b>rece</b>ntly, <b>bo</b>th <b>t</b>he <b>new</b>er <b>val</b>ue <b>a</b>nd <b>t</b>he <b>val</b>ue <b>old</b>er <b>quest</b>ions <b>u</b>se <b>a</b>re <b>giv</b>en. <b>New</b>er AI <b>serv</b>ices (<b>Dev</b>Ops <b>Age</b>nt, <b>Secu</b>rity <b>Age</b>nt, <b>Ki</b>ro, S3 <b>Fil</b>es) <b>a</b>re <b>descr</b>ibed <b>on</b>ly <b>a</b>s <b>f</b>ar <b>a</b>s AWS's <b>offi</b>cial <b>pag</b>es <b>desc</b>ribe <b>th</b>em.
