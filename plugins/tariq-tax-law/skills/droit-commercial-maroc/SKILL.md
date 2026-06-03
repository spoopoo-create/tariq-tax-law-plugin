---
name: droit-commercial-maroc
description: >-
  Expert droit commercial marocain — Code de commerce (loi 15-95, version
  consolidée intégrant la loi 21-18 sûretés mobilières et la loi 73-17
  difficultés de l'entreprise ; 5 livres : commerçant / fonds de commerce /
  effets de commerce / contrats commerciaux / difficultés de l'entreprise),
  droit des sociétés (loi 17-95 SA, loi 5-96 SARL/SNC/SCS/SCA/société en
  participation), juridictions de commerce (loi 53-95), arbitrage et médiation
  (loi 95-17), en articulation avec le DOC (régime commun des contrats),
  la fiscalité (CGI : cession de fonds, parts sociales, prépondérance
  immobilière, solidarité acquéreur/cédant) et la comptabilité (CGNC).
  Use when l'utilisateur évoque : commerçant, acte de commerce, registre du
  commerce (RC), fonds de commerce, cession / nantissement / location-gérance
  de fonds, lettre de change, billet à ordre, chèque, warrant, opérations de
  banque, compte courant, bail commercial (renouvellement, déplafonnement,
  indemnité d'éviction, pas-de-porte), vente commerciale, gage commercial,
  transport, courtage, commission, sûretés mobilières (gage sans dépossession,
  registre national des sûretés), redressement / liquidation judiciaire,
  cessation des paiements, période d'observation, plan de continuation, plan de
  cession, sauvegarde, conciliation, mandataire / syndic / juge-commissaire,
  déclaration de créance, nullités de la période suspecte, banqueroute,
  responsabilité du dirigeant, comblement de passif, SA / SARL / SAS / SNC /
  SCS / SCA, pacte d'associés, cession de parts ou d'actions, augmentation /
  réduction de capital, fusion-scission, dissolution-liquidation amiable,
  abus de biens sociaux, abus de majorité/minorité, expertise de gestion,
  juridiction de commerce (tribunal de commerce, cour d'appel de commerce),
  clause compromissoire, arbitrage commercial, exequatur de sentence.
  Use AUSSI dès qu'un dossier civil, fiscal, foncier, pénal des affaires,
  social ou de change comporte un volet commercial (qualification de
  commercialité, compétence du tribunal de commerce, sûreté commerciale,
  procédure collective) même sans que le mot « commercial » soit prononcé.
---

# droit-commercial-maroc — orchestration

## Rôle
Pilote les outils du serveur MCP **Tariq Tax & Law** pour traiter une question de droit commercial marocain (sociétés, fonds de commerce, effets de commerce, difficultés d'entreprise). Toute la matière — méthode, textes, taux, seuils, délais, jurisprudence, doctrine — vit dans le MCP, derrière l'abonnement. Cette skill ne tranche rien par elle-même : elle ordonne les appels d'outils, dans le bon ordre, et sait quand s'arrêter.

## Séquence optimale
1. `fiscal_expertise("droit-commercial-maroc")` — **UNE fois**, en tout premier. Charge le SOCLE de méthode + le cadrage du domaine (qualification, hiérarchie des sources, temporalité, charge de la preuve, livrables). Ne pas la recharger ensuite. Point de méthode pointu en cours de route → `fiscal_expertise("droit-commercial-maroc", question="…")`.
2. **Trouver les textes** → `fiscal_rechercher(query, domaine="…")`, qui renvoie des `ref`. Cibler le corpus quand c'est net (lois, adala, jurisprudence) ; sinon laisser `domaine` vide — tout le corpus, requête en français OU en arabe (correspondance bilingue automatique).
3. **Lire le texte intégral** → `fiscal_lire(ref)` sur la **seule** référence utile renvoyée en 2. La référence naturelle est acceptée (`fiscal_lire("CGI 6")`, `fiscal_lire("loi … article …")`) ; `suite=N` pour la suite d'un texte long. **Lois et codes se lisent par article, jamais en entier.**
4. **Naviguer / suivre les citations** → `fiscal_explorer(action, cible, filtre)` : inventaire et navigation, et surtout `citations_de` / `citations_vers` / `resoudre` pour relier un arrêt ou une décision aux articles qu'il vise (et l'inverse). N'appeler que si la navigation ou le graphe sert vraiment la réponse.
5. Livrable = acte de société ou consultation : fondement (loi sur les sociétés, Code de commerce) + jurisprudence + impact fiscal si demandé.
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
- Formalités et délais (registre du commerce, publicité légale, procédures collectives) vérifiés sur le texte applicable.
- Conclure (livrable rendu ou refus motivé) — pas d'arrêt à mi-parcours.
