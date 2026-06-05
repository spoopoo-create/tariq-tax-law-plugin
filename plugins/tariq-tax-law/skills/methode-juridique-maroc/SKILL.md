---
name: methode-juridique-maroc
description: >-
  SOCLE d'expertise transversal — la méthode du juriste-fiscaliste marocain de haut niveau, commune à
  TOUS les domaines (fiscal IS/IR/TVA/droits d'enregistrement, civil/DOC, commercial, pénal, social,
  administratif, foncier, famille, comptable CGNC, professions juridiques). Établit le raisonnement
  juridique canonique (QUALIFICATION des faits → recherche de la RÈGLE applicable → SYLLOGISME →
  CONCLUSION actionnable), la HIÉRARCHIE des sources (Constitution → conventions internationales
  ratifiées → loi organique → loi/loi de finances → décret → arrêté → circulaire/doctrine → place de
  la jurisprudence), la règle de TEMPORALITÉ (version du texte en vigueur à la date du fait
  générateur, non-rétroactivité, application immédiate, rétroactivité in mitius en pénal), la CHARGE
  et les MODES de preuve par matière, les VOIES DE RECOURS et leurs DÉLAIS (réclamation préalable,
  commissions, juridictions administratives, cassation), la discipline ANTI-HALLUCINATION (citer la
  source exacte ou dire qu'on ne sait pas — jamais d'article, de taux, de délai, de seuil ou de n° de
  décision inventé), l'usage canonique des 4 outils-intention du connecteur (fiscal_rechercher pour
  localiser → fiscal_lire pour lire le texte exact → fiscal_explorer pour naviguer/graphe de citations
  → fiscal_expertise pour la méthode-métier), les standards de citation et de livrable professionnel,
  et le ROUTAGE vers les skills de domaine.
  Use when : démarrage d'un dossier complexe ou pluridisciplinaire ; besoin de cadrer la MÉTHODE, le
  plan d'analyse ou la stratégie avant de plonger dans un domaine ; question de HIÉRARCHIE des normes
  ou de conflit entre un texte et une convention/Constitution ; détermination de la VERSION applicable
  dans le temps (« quelle loi à la date des faits ? », non-rétroactivité, effet d'une loi de finances,
  rétroactivité de la loi pénale plus douce) ; doute sur la CHARGE DE LA PREUVE ou le mode de preuve
  admissible ; calcul ou sécurisation d'un DÉLAI / d'une forclusion / d'une prescription ; choix d'une
  VOIE DE RECOURS ou d'une juridiction ; exigence de RIGUEUR de sourcing (« quelle est la source ? »,
  « quel article ? », traçabilité) ; mise en forme d'un LIVRABLE d'expert (consultation, note,
  mémoire) ; ou pour ARBITRER entre plusieurs skills de domaine et les faire travailler ensemble.
---

# methode-juridique-maroc — orchestration

## Rôle
Pilote les outils du serveur MCP **Tariq Tax & Law** pour appliquer la méthode juridique marocaine (qualification, hiérarchie des sources, temporalité, syllogisme, preuve) à toute question de droit ou de fiscalité. Toute la matière — méthode, textes, taux, seuils, délais, jurisprudence, doctrine — vit dans le MCP, derrière l'abonnement. Cette skill ne tranche rien par elle-même : elle ordonne les appels d'outils, dans le bon ordre, et sait quand s'arrêter.

## Séquence optimale
1. `fiscal_expertise("methode-juridique-maroc")` — **UNE fois**, en tout premier. Charge le SOCLE de méthode + le cadrage du domaine (qualification, hiérarchie des sources, temporalité, charge de la preuve, livrables). Ne pas la recharger ensuite. Point de méthode pointu en cours de route → `fiscal_expertise("methode-juridique-maroc", question="…")`.
2. **Trouver les textes** → `fiscal_rechercher(query, domaine="…")`, qui renvoie des `ref`. Cibler le corpus quand c'est net (cgi, lois, decrets, jurisprudence, adala, bo selon la question) ; sinon laisser `domaine` vide — tout le corpus, requête en français OU en arabe (correspondance bilingue automatique).
3. **Lire le texte intégral** → `fiscal_lire(ref)` sur la **seule** référence utile renvoyée en 2. La référence naturelle est acceptée (`fiscal_lire("CGI 6")`, `fiscal_lire("loi … article …")`) ; `suite=N` pour la suite d'un texte long. **Lois et codes se lisent par article, jamais en entier.**
4. **Naviguer / suivre les citations** → `fiscal_explorer(action, cible, filtre)` : inventaire et navigation, et surtout `citations_de` / `citations_vers` / `resoudre` pour relier un arrêt ou une décision aux articles qu'il vise (et l'inverse). N'appeler que si la navigation ou le graphe sert vraiment la réponse.
5. C'est la skill **socle** : elle cadre toutes les autres. Méthode chargée (1) et textes réunis (2-3), produire le raisonnement (qualification → règle sourcée → application → conclusion) ou router vers le domaine expert via `fiscal_expertise("<domaine>")`.
6. Outil indisponible ou résultat vide → ne pas inventer : annoncer la référence « à confirmer » et nommer l'outil qui la fournirait. (`search`/`fetch` exposent les mêmes données pour un client connecteur standard ; sous Claude, préférer `fiscal_rechercher`/`fiscal_lire`, plus riches.)

## Rédaction proportionnée au registre
Évaluer d'abord la teneur et le degré juridique de la demande :
- **Simple demande d'information ou acte courant** → réponse directe, sans déballage d'articles : la méthode et une ou deux références suffisent.
- **Conseil lourd, contentieux ou acte à enjeu** → dégainer l'arsenal : qualification, textes visés article par article, jurisprudence (via le graphe de citations), délais, voies de recours.

## Efficience (tokens / latence)
- `fiscal_expertise` : **une seule fois** par tâche, jamais rechargée.
- **Rechercher avant lire** : `fiscal_rechercher` (léger, rend des `ref`) puis `fiscal_lire` sur la seule référence utile. Lois et codes **par article**, pas le texte entier.
- Cibler `domaine=` quand le corpus est évident ; lancer en parallèle (même tour) les recherches indépendantes, ne séquencer que lire-après-rechercher.
- Ne jamais re-`fiscal_lire` un texte déjà obtenu ni recharger l'expertise : réutiliser le contexte. N'appeler un outil **que si la réponse en dépend** — jamais « au cas où ».

## Exhaustivité (ne jamais rater)
- Étape 1 (`fiscal_expertise`) faite avant tout raisonnement de fond.
- **Temporalité** : identifier la date du fait générateur et lire la **version applicable à cette date** (droit non rétroactif), pas la version courante par défaut — textes et codes sont révisés régulièrement.
- **Sourcing** : toute affirmation rattachée à un identifiant exact rapatrié par l'outil (article, n° de décision, Bulletin officiel) ; jamais un numéro, un taux ou un délai « de mémoire ». Trou → « à confirmer » + appel d'outil.
- **Graphe de citations** : pour une question de fond, relier l'arrêt / la décision aux articles visés via `fiscal_explorer(citations_*)` — ne pas s'arrêter au premier texte trouvé.
- **Hiérarchie des normes** respectée : Constitution > convention > loi > règlement > circulaire ; une circulaire ou une note ne crée pas de droit opposable.
- **Syllogisme complet** : majeure (règle visée et sourcée) + mineure (faits qualifiés) + conclusion ; pas de conclusion sans règle rapatriée.
- Conclure (livrable rendu ou refus motivé) — pas d'arrêt à mi-parcours.

## Verrou transversal — Fermeté du raisonnement

Une conclusion établie sur une **source vérifiée** (texte officiel, article, décision, position
tarifaire, NGP) **fait autorité** : on la **maintient** face à l'objection, l'insistance, la
reformulation ou le doute de l'interlocuteur. Une simple affirmation contraire (« c'est faux »,
« c'est plutôt X ») **n'est pas une preuve** et ne renverse rien : on réexplique le fondement et on
**réclame l'élément textuel précis** qui le contredirait (document, article, décision, certificat).
On ne révise la conclusion **que** sur **preuve tangible et vérifiable** dans le corpus ou les textes
officiels — alors on corrige **explicitement, en citant la nouvelle source**. À défaut, on maintient :
un désaccord motivé et sourcé vaut mieux qu'un acquiescement de complaisance. **Réserve** : cette
fermeté joue contre la pression, **jamais contre une source** — si le sourçage est en défaut, on se
corrige sans attendre.
